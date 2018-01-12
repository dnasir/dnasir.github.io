---
layout: post
title: Localising your JavaScript in ASP.NET
date: 2013-01-11 18:13
comments: true
categories: [ASP.NET, asp.net, JavaScript, javascript, localisation, localization, Web Development]
---
Most modern websites use JavaScript to display some form of text, be it for client-side validation or just simple alerts and notifications. While this is fine on a single language website, it can cause some major headaches with multilingual ones.

ASP.NET has provided a way to implement localisation using resource files, and while this is great for web forms and Razor views, the same can't be said about JavaScript codes.

One solution would be to create a JavaScript translation file that contains localised text for every language you're planning to support. But since I'm already using resource files, why not leverage that and use them to localise my JavaScript?

Mads Kristensen has posted a very interesting solution for developers who want to use resource files for localising their JavaScript code. You can see it <a title="Localize text in JavaScript files in ASP.NET" href="http://madskristensen.net/post/Localize-text-in-JavaScript-files-in-ASPNET.aspx" target="_blank">here</a>.

I simply modified his idea to work with an MVC project I was working on. So, while Mads' solution works on the idea that the appropriate language be applied based on the user's browser language, I've extended his idea to allow the handler to accept a language code parameter which determines the language I want my JavaScript to be translated into.

Here's what my code looks like. The rest is pretty much the same as Mads' solution.

Feel free to use and abuse.

[csharp]
using System;
using System.Globalization;
using System.IO;
using System.IO.Compression;
using System.Resources;
using System.Text.RegularExpressions;
using System.Threading;
using System.Web;
using Swarco.Resources.Views;


public class ScriptLocalisation : IHttpHandler
{
    private const string Gzip = "gzip";
    private const string Deflate = "deflate";
    private static Regex RegEx = new Regex(@"Translate\(([^\))]*)\)", RegexOptions.Singleline | RegexOptions.Compiled);
    public bool IsReusable
    {
        get
        {
            return false;
        }
    }

    public void ProcessRequest(HttpContext context)
    {
        try
        {
            context.Response.Write("// test" + Environment.NewLine);

            var languageCode = context.Request["lang"];
            var relativePath = context.Request.AppRelativeCurrentExecutionFilePath;
            var absolutePath = context.Server.MapPath(relativePath);
            var axdIndex = absolutePath.LastIndexOf(".axd", StringComparison.Ordinal);
            absolutePath = absolutePath.Substring(0, axdIndex);
            var script = ReadFile(absolutePath);

            if (!string.IsNullOrWhiteSpace(languageCode))
            {
                Thread.CurrentThread.CurrentUICulture = CultureInfo.CreateSpecificCulture(languageCode);
            }

            var translated = TranslateScript(script);

            context.Response.Write(translated);

            Compress(context);
            SetHeadersAndCache(absolutePath, context);
        }
        catch (Exception ex)
        {
            context.Response.Write(ex);
        }

        context.Response.Flush();
    }

    /// <summary>
    ///     This will make the browser and server keep the output
    ///     in its cache and thereby improve performance.
    /// </summary>
    private void SetHeadersAndCache(string file, HttpContext context)
    {
        context.Response.ContentType = "text/javascript";
        context.Response.AddFileDependency(file);
        context.Response.Cache.VaryByHeaders["Accept-Language"] = true;
        context.Response.Cache.VaryByHeaders["Accept-Encoding"] = true;
        context.Response.Cache.SetLastModifiedFromFileDependencies();
        context.Response.Cache.SetExpires(DateTime.Now.AddDays(7));
        context.Response.Cache.SetValidUntilExpires(true);
        context.Response.Cache.SetCacheability(HttpCacheability.Public);
    }

    /// <summary>
    ///     Translates the text keys in the script file. The format is Translate(key).
    /// </summary>
    /// <param name="text">The text in the script file.</param>
    /// <returns>A localized version of the script</returns>
    private string TranslateScript(string text)
    {
        var matches = RegEx.Matches(text);
        var manager = new ResourceManager(typeof(JavaScript));

        foreach (Match match in matches)
        {
            var obj = manager.GetObject(match.Groups[1].Value);
            text = text.Replace(match.Value, obj != null ? CleanText(obj.ToString()) : string.Empty);
        }

        return text;
    }

    /// <summary>
    ///     Cleans the localized script text from making invalid javascript.
    /// </summary>
    private static string CleanText(string text)
    {
        text = text.Replace("'", "\\'");
        text = text.Replace("\\", "\\\\");

        return text;
    }

    /// <summary>
    ///     Reads the content of the specified file.
    /// </summary>
    private static string ReadFile(string absolutePath)
    {
        if (File.Exists(absolutePath))
        {
            using (var reader = new StreamReader(absolutePath))
            {
                return reader.ReadToEnd();
            }
        }

        return null;
    }

    /// <summary>
    ///     Compresses the HTTP response.
    /// </summary>
    private static void Compress(HttpContext context)
    {
        if (IsEncodingAccepted(Deflate, context))
        {
            context.Response.Filter = new DeflateStream(context.Response.Filter, CompressionMode.Compress);
            SetEncoding(Deflate, context);
        }
        else if (IsEncodingAccepted(Gzip, context))
        {
            context.Response.Filter = new GZipStream(context.Response.Filter, CompressionMode.Compress);
            SetEncoding(Gzip, context);
        }
    }

    /// <summary>
    ///     Checks the request headers to see if the specified
    ///     encoding is accepted by the client.
    /// </summary>
    private static bool IsEncodingAccepted(string encoding, HttpContext context)
    {
        return context.Request.Headers["Accept-encoding"] != null && context.Request.Headers["Accept-encoding"].Contains(encoding);
    }

    /// <summary>
    ///     Adds the specified encoding to the response headers.
    /// </summary>
    private static void SetEncoding(string encoding, HttpContext context)
    {
        context.Response.AppendHeader("Content-encoding", encoding);
    }
}
[/csharp]
