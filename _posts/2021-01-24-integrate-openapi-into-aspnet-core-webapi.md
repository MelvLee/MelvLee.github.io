---
layout: post
title: Integrate OpenAPI into your ASP.NET Core Web API
css: ['/assets/css/style.css']
tags: [OpenAPI, Swashbuckle, ASP.NET Core Web API]
---
Nowadays, it is a best practice to use the [OpenAPI Specification][1] (OAS) also known as Swagger, to describe the capabilities of your Web API. With an OAS description document:

- you can use tools like [swagger-ui][2] ([example](https://petstore.swagger.io/)) or [redoc][6] ([example](https://redocly.github.io/redoc/)) to generate interactive documentation for your Web API
- your consumers can use code generators like [OpenAPI Generator][3] to generate client code to access your Web API

## Generate an OpenAPI description document from code

If you have implemented your Web API in [.NET Core][4] using the ASP.NET Core Web API template, you can use [Swashbuckle][5] to generate:

- a OAS description document from your Web API code
- interactive documentation for your Web API using swagger-ui or redoc

OpenAPI support is enabled by default for a ASP.NET Core 5 Web API project. If you have disabled it, you can enable it again using the following steps. The same steps also apply for a ASP.NET Core 2 and 3 Web API project:

1. install the **Swashbuckle.AspNetCore** NuGet package
2. call the **AddSwaggerGen** method in Startup.ConfigureServices to register Swashbuckle's OAS generator
3. in Startup.Configure, call the **UseSwagger** method to add the middleware to expose the generated OAS description document. By default, the generated OAS description document is available at *[baseurl]/swagger/v1/swagger.json*
4. call the **UseSwaggerUI** method in Startup.Configure to add the middleware to generate documentation using swagger-ui. The generated documentation will be available at *[baseurl]/swagger*

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // 2. register OAS generator
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "Net5WebApi", Version = "v1" });
    });
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    // 3. add middleware to expose generated OAS description document
    app.UseSwagger();

    // 4. add middleware to expose documentation generated with swagger-ui
    app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API"));

    ...

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}

```

## Integrate a handcrafted OAS description document in your Web API

OAS description documents can also be created by hand using tools like the [Swagger Editor][7]. If you have such a handcrafted OAS description document, you can use a code generator like the [OpenAPI Generator][3] to generate the boilerplate code for your Web API and use [Swashbuckle][5] to generate the interactive documentation with swagger-ui.

If the handcrafted OAS description document is accessable via an url then all you have to do is assign this url to the *url* parameter of the SwaggerEndpoint method mentioned in step 4.

``` c#
app.UseSwaggerUI(c => c.SwaggerEndpoint("[url of handcrafted OAS description document]", "My API"));
```

Omit step 2 and 3, since it is not necessary to generate an OAS decription document from code.

If the handcrafted OAS description document for your Web API is not accessable via an url, you can expose it via your Web API using the following steps:

1. create the **wwwroot** folder in your Web API project
2. copy the handcrafted OAS description document to this folder
3. call the **UseStaticFiles** method in Startup.Configure to enable static file serving
4. create a file extension to mimetype mapping using a FileExtensionContentTypeProvider if the OAS description document is a yaml file. The handcrafted OAS description document in the wwwroot folder is now accessable via *[baseurl]/[name of handcrafted OAS description document]
5. assign the relative url of the handcrafted OAS description document to the *url* parameter of the SwaggerEndpoint method

``` c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
  ...

  // json variant of OAS description document
  // 3. add middleware to serve static files
  app.UseStaticFiles();
  // 5. add middleware to expose documentation generated with swagger-ui. The name of the OAS description document is openapi.json
  app.UseSwaggerUI(c => c.SwaggerEndpoint("/openapi.json", "My API"));


  // yaml variant of OAS description document
  // 4. create '.yaml - application/x-yaml' mapping to enable serving of yaml files via your Web API
  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings.Add(".yaml", "application/x-yaml");
  // 3. add middleware to serve static files
  app.UseStaticFiles(new StaticFileOptions
  {
    ContentTypeProvider = provider
  });
  // 5. add middleware to expose documentation generated with swagger-ui. The name of the OAS description document is openapi.yaml
  app.UseSwaggerUI(c => c.SwaggerEndpoint("/openapi.yaml", "My API"));

  ...
}
```

## Conclusion

If you already have a Web API implemented using ASP.NET Core 2 and higher, you can use Swashbuckle to generate an OpenAPI description document for your Web API. With the OpenAPI description document, you can use Swashbuckle to generate beautiful interactive documentation for your Web API.  
You will make it easier for your existing and future consumers to discover your Web API capabilities and generate client code to use the capabilities of your Web API. Check Swashbuckle's documentation to learn about other features. These features can increase the quality of your OpenAPI description document and with that also your Web API documentation.

[1]: https://spec.openapis.org/oas/v3.0.3
[2]: https://swagger.io/tools/swagger-ui/
[3]: https://openapi-generator.tech/
[4]: https://dotnet.microsoft.com/apps/aspnet/apis
[5]: https://github.com/domaindrivendev/Swashbuckle.AspNetCore
[6]: https://redoc.ly/redoc
[7]: https://swagger.io/tools/swagger-editor/
