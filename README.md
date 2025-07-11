# dotnet-api-documentation

1. Create a Web API project<br><br>
2. Install the NuGet Packages: EntityFrameworkCore, EntityFrameworkCore.Design, EntityFrameworkCore.Tools, EntityFrameworkCore.SQLServer (se for usar sql server)
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

6. Adciione o Service do DbContext no Program.cs
```
builder.Services.AddDbContext<AppDbContext>(options => {
  options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
})
```

7. Execute as migrações no Console do Gerenciador de Pacotes
```
add-migration NameOfYourMigration
// confira os arquivos criados
update-database
```

8. 
