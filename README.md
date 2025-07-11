# dotnet-api-documentation

1. Create a Web API project<br><br>
2. Install the NuGet Packages: EntityFrameworkCore, EntityFrameworkCore.Design, EntityFrameworkCore.Tools, EntityFrameworkCore.SQLServer (se for usar sql server)<br><br>
3. Crie a pasta para o DbContext e cria uma classe pra isso

```
using Microsoft.EntityFrameworkCore;

namespace SeuProjeto.Data
{
  public class AppDbContext : DbContext
  {
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) {}

    DbSet<YourModel> YourTableName { get; set; }
  }
}
```
<br><br>
4. Crie seu model numa pasta Models ou Entities
```
namespace SeuProjeto.Models
{
  public class YourModel
  {
    public int Id { get; set; }
    public string Nome { get; set; }
    ...
  }
}
```
<br><br>
5. Configure a Connection String no appsettings.json
```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": Warning
    }
  },
  "AllowHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "server=your-server-name-on-sql-server; database=nome-para-o-database; trusted_connection=true; trustservercertificate=true"
  }
}
```
<br><br>
6. Adciione o Service do DbContext no Program.cs
```
builder.Services.AddDbContext<AppDbContext>(options => {
  options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
})
```
<br><br>
7. Execute as migrações no Console do Gerenciador de Pacotes
```
add-migration NameOfYourMigration
// confira os arquivos criados
update-database
```

8. Criar os endpoints adicionando um Controlador API - Vazio na pasta Controllers
```
using Microsoft.AspNetCore.Mvc;

namespace YourProject.Controllers
{
  [Route("api/[controller]")]
  [ApiController]

  public class YourModelController : ControllerBase
  {
    private readonly AppDbContext _context;

    public YourModelController(DbContext context)
    {
      _context = context;
    }

    [HttpGet]
    public ActionResult<List<YourModel>> GetYourModels()
    {
      var yourModels = _context.YourTableName.ToList();
      return ok(yourModels);
    }

    [HttpGet]
    public ActionResult<YourModeL> GetYourModelById(int id)
    {
      var produto = _context.YourTableName.Find(id);

      if (produto == null)
      {
        return NotFound("Your message of not found.");
      }

      return Ok(produto);
    }

    

  }
}
```


## Para testar os endpoint você pode usar o Scalar

1. Instale o NuGet Package Scalar.AspNetCore <br><br>

2. Adicionar no app no Program.cs
```
using Scalar.AspNetCore;

if (app.Environment.IsDevelopment())
{
  app.MapScalarApiReference();
  app.MapOpenApi();
}
```
<br><br>

3. No launchSettings.json da pasta Properties veja qual a url seu projeto está rodando em "applicationUrl" e digite: url + scalar/v1
