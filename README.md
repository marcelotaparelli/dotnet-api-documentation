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
6. Adicione o Service do DbContext no Program.cs
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

    public YourModelController(AppDbContext context)
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
    [Route("{id}")]
    public ActionResult<YourModeL> GetYourModelById(int id)
    {
      var produto = _context.YourTableName.Find(id);

      if (produto == null)
      {
        return NotFound("Your message of not found.");
      }

      return Ok(produto);
    }

    [HttpPost]
    public ActionResult<YourModel> CreateYourModel(YourModel yourModel)
    {
      if(yourModel == null)
      {
        return BadRequest("Your message of bad request");
      }

      _context.YourTableName.Add(yourModel);
      _context.SaveChanges();

      return CreatedAtAction(nameof(GetYourModelById), new { id = yourModel.id }, yourModel);
    }

    [HttpPut]
    [Route("{id}")]
    public ActionResult<YourModel> UpdateYourModel(YourModel yourModel, int id)
    {
      var produto = _context.YourTableName.Find(id);

      if(yourModel == null)
      {
        return NotFound("Your notfound message");
      }

      produto.Nome = yourModel.Nome;
      ... //atualize as propriedades

      _context.YourTableName.Update(produto);
      _context.SaveChanges();

      return NoContent();
    }        

    [HttpDelete]
    public ActionResult<YourModel> DeleteYourModel(int id)
    {
      var produto = _context.YourTableName.Find(id);

      if (produto == null)
      {
        return NotFound("Your notfound message.");
      }

      _context.YourTableName.Remove(produto);
      _context.SaveChanges();

      return NoContent();
    }

  }  
}
```
<br><br><br><br>

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

<br><br><br>

## Você também pode usar o arquivo .http do .net9

```
@host = http://localhost:5011

### 🔍 GET - Listar todos os contatos
GET {{host}}/api/contatos
Accept: application/json

###

### 🔍 GET - Obter contato por ID
GET {{host}}/api/contatos/1
Accept: application/json

###

### ➕ POST - Criar novo contato
POST {{host}}/api/contatos
Content-Type: application/json

{
  "nome": "Marcelo Silva",
  "email": "marcelo@example.com",
  "telefone": "11999999999"
}

###

### 📝 PUT - Atualizar contato por ID (substituição completa)
PUT {{host}}/api/contatos/1
Content-Type: application/json

{
  "nome": "Marcelo Atualizado",
  "email": "marcelo.novo@example.com",
  "telefone": "11888888888"
}

###

### ✏️ PATCH - Atualizar parcialmente (ex: só o email)
PATCH {{host}}/api/contatos/1
Content-Type: application/json

{
  "email": "marcelo.atualizado@example.com"
}

###

### ❌ DELETE - Remover contato por ID
DELETE {{host}}/api/contatos/1
```


<br><br><br>

## Configure o CORS

https://learn.microsoft.com/pt-br/aspnet/core/security/cors?view=aspnetcore-9.0
