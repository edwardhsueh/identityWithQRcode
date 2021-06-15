1. Create a MVC Project(with Database) withOut Auth.
2. Scaffold Identity in ASP.NET Core projects
    https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-5.0&tabs=netcore-cli
    
    2.1 dotnet tool install -g dotnet-aspnet-codegenerator
    2.2
    dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
    dotnet add package Microsoft.EntityFrameworkCore.Design
    dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
    dotnet add package Microsoft.AspNetCore.Identity.UI
    dotnet add package Microsoft.EntityFrameworkCore.SqlServer
    dotnet add package Microsoft.EntityFrameworkCore.Tools    
    2.3 Create Database
        Open Area/Identity/IdentityHostingStartUp.cs and find DbContextName
        dotnet ef migrations add CreateIdentitySchema --context IdentityAppIdentityDbContext  
        dotnet ef database drop --force  --context IdentityAppIdentityDbContext
        dotnet ef database update
    2.4 check Startup.cs
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddControllersWithViews();
                services.AddRazorPages();
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                    app.UseDatabaseErrorPage();
                }
                else
                {
                    app.UseExceptionHandler("/Home/Error");
                    app.UseHsts();
                }
                app.UseHttpsRedirection();
                app.UseStaticFiles();

                app.UseRouting();

                app.UseAuthentication();
                app.UseAuthorization();

                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapControllerRoute(
                        name: "default",
                        pattern: "{controller=Home}/{action=Index}/{id?}");
                    endpoints.MapRazorPages();
                });
            }
        }    


3.  Enable QRCode
    Refrence https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-enable-qrcodes?view=aspnetcore-5.0
    
    1. dotnet aspnet-codegenerator identity -fi Account.Manage.EnableAuthenticator
    2. libman install qrcodejs
    3. open Area/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml and 
    3.1 Comment
        <div class="alert alert-info">Learn how to <a href="https://go.microsoft.com/fwlink/?Linkid=852423">enable QR code generation</a>.</div>

    3.2 modify @section Scripts to the following

    @section Scripts {
        @await Html.PartialAsync("_ValidationScriptsPartial")

        <script type="text/javascript" src="~/lib/qrcodejs/qrcode.js"></script>
        <script type="text/javascript">
            new QRCode(document.getElementById("qrCode"),
                {
                    text: "@Html.Raw(Model.AuthenticatorUri)",
                    width: 150,
                    height: 150
                });
        </script>
    }