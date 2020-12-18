#  CSharp-MVC-Project-Steps

## 1: dotnet new mvc --no-https -o {ProjectName}
cd ProjectName
code .

## 2: Entity FrameWork
#### --Add Package:
```
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 3.1.1
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.5
```

#### --Make Models:

EXAMPLE: IN (Models/{Monster}.cs)
```
using System;
using System.ComponentModel.DataAnnotations;
namespace {Monsters}.Models
{
    public class {Monster}
    {
        [Key]
        public int MonsterId { get; set; }
        public string Name { get; set; }
        public int Height { get; set; }
        public string Description { get; set; }
        public DateTime CreatedAt { get; set; } = DateTime.Now;
        public DateTime UpdatedAt { get; set; } = DateTime.Now;
    }
}
```

#### --Make MyContext.cs in Models:

IN (Models/MyContext.cs)
```
using Microsoft.EntityFrameworkCore;
namespace {Monster}.Models
{ 
    // the MyContext class representing a session with our MySQL 
    // database allowing us to query for or save data
    public class MyContext : DbContext 
    { 
        public MyContext(DbContextOptions options) : base(options) { }
        // the "Monsters" table name will come from the DbSet variable name
        public DbSet<Monster> Monsters { get; set; }
    }
}
```

#### --Set up Json, remember to change the password and database
```
"DBInfo": //don't forget "," in the begining line
    {
        "Name": "MySQLconnect",
        "ConnectionString": "server=localhost;userid=root;password=root;port=3306;database=db;SslMode=None"
    }
```

#### -- Startup.cs(It is better to change the startup.cs later to avoid errors)

Startup.cs should now be:
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using {ProjectName}.Models; //get access to MyContext
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;

namespace {ProjectName}
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }
        public IConfiguration Configuration { get; }
        public void ConfigureServices(IServiceCollection services)
        {
            //we'll use this for logging in and registering
            services.AddSession();
            services.AddDbContext<MyContext>(options => options.UseMySql(Configuration["DBInfo:ConnectionString"]));
            //used for routing
            services.AddMvc(options => options.EnableEndpointRouting = false);
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseStaticFiles();

            app.UseSession(); //use session first 
            app.UseMvc(); //always put usemvc at bottom

            // app.UseRouting();

            // app.UseEndpoints(endpoints =>
            // {
            //     endpoints.MapGet("/", async context =>
            //     {
            //         await context.Response.WriteAsync("Hello World!");
            //     });
            // });
        }
    }
}
```

#### --Set up HomeController.cs

```
namespace {Monsters}.Controllers
{
    public class HomeController : Controller
    {
        private MyContext _context;
     
        // here we can "inject" our context service into the constructor
        public HomeController(MyContext context)
        {
            _context = context;
        }
     
        [HttpGet"")]
        public IActionResult Index()
        {
            List<Monster> AllMonsters = _context.Monsters.ToList();
            
            return View();
        }
    }
 }
 ```

#### --Migrate
Kill the terminal and run:
```
dotnet ef migrations add YourMigrationName
dotnet ef database update
```

## 3: Creating a new table:
1. set up the Model class
2. add it to Mycontext
3. create migration: dotnet ef migrations add YourMigrationName
4. update the DB: dotnet ef database update


## 4: Template:
```
<!DOCTYPE html>
<html>

<head>
    <meta charset='utf-8'>
    <title>Hello!</title>
</head>

<body>
    
</body>
</html>
```
