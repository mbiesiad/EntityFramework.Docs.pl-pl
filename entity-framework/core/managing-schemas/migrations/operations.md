---
title: Operacje migracji niestandardowe — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 93de6ee1b2eda1875188ace6eda299260fbcc1fe
ms.sourcegitcommit: 082946dcaa1ee5174e692dbfe53adeed40609c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51028086"
---
<a name="custom-migrations-operations"></a>Operacje migracji niestandardowe
============================
Interfejs API MigrationBuilder umożliwia wykonywać wiele różnych operacji podczas migracji, ale go nie jest wyczerpująca. Interfejs API jest jednak również rozszerzalny, co pozwala zdefiniować własne operacje. Istnieją dwa sposoby, aby rozszerzyć interfejs API: przy użyciu `Sql()` metody lub przez zdefiniowanie niestandardowego `MigrationOperation` obiektów.

Aby zilustrować, Przyjrzyjmy się wykonania operacji, która tworzy użytkownika bazy danych za pomocą danej metody. W naszym migracje chcemy włączenia zapisywania następujący kod:

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a>Za pomocą MigrationBuilder.Sql()
----------------------------
Najłatwiejszym sposobem realizowania operacji niestandardowej jest zdefiniowanie metodę rozszerzającą wywołująca `MigrationBuilder.Sql()`.
Oto przykład, który generuje odpowiednie języka Transact-SQL.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

Jeśli migracji potrzebne do obsługi wielu dostawców bazy danych, można użyć `MigrationBuilder.ActiveProvider` właściwości. Oto przykład obsługi zarówno programu Microsoft SQL Server, jak i bazy danych PostgreSQL.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

To podejście tylko wtedy, gdy wiesz, co dostawcy której zostaną zastosowane niestandardowych operacji.

<a name="using-a-migrationoperation"></a>Za pomocą MigrationOperation
---------------------------
Aby oddzielić niestandardowych operacji z bazy danych SQL, można zdefiniować własne `MigrationOperation` go reprezentuje. Operacja są następnie przekazywane do dostawcy, dzięki czemu można określić, odpowiednie SQL w celu generowania.

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

W przypadku tej metody, metoda rozszerzenia musi jedynie jedną z tych operacji, aby dodać `MigrationBuilder.Operations`.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

Takie podejście wymaga każdego dostawcy wiedzieć, jak wygenerować kodu SQL do wykonania tej operacji w ich `IMigrationsSqlGenerator` usługi. Oto przykład zastępowanie generator programu SQL Server do obsługi nowej operacji.

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

Zaktualizowano zastąpić domyślnej migracji sql generator usługi.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
