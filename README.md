# CSharp-MVC-Project-Steps

## 1:dotnet new mvc --no-https -o {ProjectName}

## 2: Startup.cs
 public class Startup
    {
 
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSession();
            services.AddMvc(options => options.EnableEndpointRouting = false);   
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            
            app.UseSession(); //use session first 
            app.UseMvc(); //always put usemvc at bottom

## 3: Entity FrameWork
#### --Add Package:
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 3.1.1
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.5

#### --Make Models:

EXAMPLE: IN (Models/Monster.cs)

using System;
using System.ComponentModel.DataAnnotations;
namespace Monsters.Models
{
    public class Monster
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

#### --Make MyContext.cs in Models:

IN (Models/MyContext.cs)

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

#### --Set up Json, remember to change the password and database

"DBInfo": //don't forget, in the begining line
    {
        "Name": "MySQLconnect",
        "ConnectionString": "server=localhost;userid=root;password=root;port=3306;database=db;SslMode=None"
    }

#### -- Update Startup.cs
Add this line:  services.AddDbContext<MyContext>(options => options.UseMySql (Configuration["DBInfo:ConnectionString"]));

Startup.cs should now be:

using {Monster}.Models;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
namespace Monsters
{
    public class Startup
    {
        public Startup (IConfiguration configuration)
        {
            Configuration = configuration;
        }
        public IConfiguration Configuration { get; }
        public void ConfigureServices (IServiceCollection services)
        {   
            services.AddSession();
            services.AddDbContext<MyContext>(options => options.UseMySql (Configuration["DBInfo:ConnectionString"]));
            services.AddMvc(options => options.EnableEndpointRouting = false);
        }
        public void Configure (IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseSession();
            app.UseStaticFiles();
            app.UseMvc();
        }
    }
}

#### --Set up HomeController.cs


namespace Monsters.Controllers
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

#### --Migrate
Kill the terminal and run:
dotnet ef migrations add YourMigrationName
dotnet ef database update

## 4: MySQL 

1: mysql -u root -p
2: show databases; (don't forget the ;)
3: use {database-name}
4: show tables;

mysql> show tables;
+-----------------------+
| Tables_in_dishdb      |
+-----------------------+
| __efmigrationshistory |
| dishes                |
+-----------------------+
2 rows in set (0.00 sec)

5: select * from {dishes}; (dishes comes from the show tables;)


## 5: Creating a new table:
1. set up the Model class
2. add it to Mycontext
3. create migration
4. update the DB


## 6: Template:
<!DOCTYPE html>
<html>

<head>
    <meta charset='utf-8'>
    <title>Hello!</title>
</head>

<body>
    
</body>
</html>
