# 2 часть
## Контекст базы данных для социальной сети используя Entity Framework Core (EF Core) с базой данных PostgreSQL

using System;
using System.Collections.Generic;
using Cogni.Database.Entities;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Npgsql.EntityFrameworkCore.PostgreSQL.Metadata;

namespace Cogni.Database.Context;

Разберем подключенные пространства:

using System импортирует пространство имен System, которое содержит основные классы и структуры 
using System.Collections.Generic импортирует пространство имен System.Collections.Generic, которое содержит интерфейсы и классы для работы с обобщенными коллекциями (например, List<T>, Dictionary<TKey, TValue>).

using Cogni.Database.Entities импортирует пространство имен Cogni.Database.Entities здесь определены классы, представляющие таблицы в базе данных (например, User, Post, Article)

using Microsoft.EntityFrameworkCore импортирует основное пространство имен для Entity Framework Core (EF Core). EF Core — это ORM (Object-Relational Mapper), который позволяет  работать с базой данных, используя объекты C# вместо SQL.

using Microsoft.Extensions.Configuration импортирует пространство имен для работы с конфигурацией приложения (например, чтение настроек из файла appsettings.json).
using Npgsql.EntityFrameworkCore.PostgreSQL.Metadata; импортирует пространство имен, специфичное для работы с PostgreSQL в EF Core. Npgsql — это провайдер данных, позволяющий EF Core взаимодействовать с базой данных PostgreSQL.

namespace Cogni.Database.Context определяет пространство имен для этого файла. Пространства имен помогают организовать код и избежать конфликтов имен, этот код относится к части, связанной с базой данных и контекстом EF Core.

Далее разберем основную часть кода

public partial class CogniDbContext : DbContext

public partial class CogniDbContext : DbContext: объявляет класс CogniDbContext, который наследуется от DbContext.
public означает, что класс доступен из любого места в проекте.

partial: partial позволяет разделить определение класса на несколько файлов. Это часто используется, когда код генерируется автоматически (например, при работе с EF Core).
CogniDbContext: Имя класса. Он представляет контекст базы данных. DbContext - это базовый класс EF Core, который предоставляет API для работы с базой данных.

    public CogniDbContext(DbContextOptions<CogniDbContext> options, IConfiguration config)
        : base(options)
    {
        connectionString = config["ConnectionStrings:PostgreSQLConnection"];
    }

    public CogniDbContext(DbContextOptions<CogniDbContext> options, IConfiguration config): Определяет конструктор, который принимает два параметра:


DbContextOptions<CogniDbContext> options позволяет настраивать параметры контекста базы данных (например, включение/выключение отслеживания изменений, использование прокси-серверов и т.д.).

IConfiguration config предоставляет доступ к конфигурационным настройкам приложения (например, к данным из appsettings.json). Это механизм для получения строки подключения из файла конфигурации.

base(options) вызывает конструктор базового класса (DbContext), передавая ему параметры конфигурации.

connectionString = config["ConnectionStrings:PostgreSQLConnection"]; считывает строку подключения из конфигурации. config["ConnectionStrings:PostgreSQLConnection"] получает значение настройки с именем “PostgreSQLConnection” в разделе “ConnectionStrings” файла конфигурации (например, appsettings.json). Полученное значение присваивается переменной connectionString.

    public virtual DbSet<Article> Articles { get; set; }

    public virtual DbSet<ArticleImage> ArticleImages { get; set; }

    public virtual DbSet<Avatar> Avatars { get; set; }

    public virtual DbSet<Chat> Chats { get; set; }

    public virtual DbSet<ChatMember> ChatMembers { get; set; }

    public virtual DbSet<User> Users { get; set; }

    public virtual DbSet<Friend> Friends { get; set; }

    public virtual DbSet<Like> Likes { get; set; }

    public virtual DbSet<MbtiQuestion> MbtiQuestions { get; set; }

    public virtual DbSet<MbtiType> MbtiTypes { get; set; }

    public virtual DbSet<Message> Messages { get; set; }

    public virtual DbSet<MessageStatus> MessageStatus { get; set; }

    public virtual DbSet<Post> Posts { get; set; }

    public virtual DbSet<PostImage> PostImages { get; set; }

    public virtual DbSet<Role> Roles { get; set; }

    public virtual DbSet<Tag> Tags { get; set; }

    public virtual DbSet<UserTag> UserTags { get; set; }

    public virtual DbSet<Article> Articles { get; set; }: Объявляет свойство Articles типа DbSet<Article>.


virtual позволяет переопределять свойство в производных классах 

DbSet<Article>: DbSet - это класс EF Core, который представляет набор сущностей определенного типа.  

DbSet<Article> представляет все статьи в базе данных. Он позволяет выполнять запросы к таблице “Articles”, добавлять новые статьи, обновлять существующие статьи и удалять статьи.

Articles: имя свойства. По соглашению, имя свойства соответствует имени таблицы в базе данных.

{ get; set; }: автоматически реализованные свойства. get позволяет получить доступ к данным, а set позволяет установить данные.

Аналогичное применяется ко всем другим свойствам DbSet, представляющим различные таблицы в базе данных

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        MigrationsAssembly("Cogni");

protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder): Переопределяет метод OnConfiguring из базового класса DbContext. Этот метод вызывается EF Core при настройке контекста базы данных.

protected метод доступен внутри этого класса и в производных классах.
override указывает, что метод переопределяет метод из базового класса.
void метод не возвращает никакого значения.
OnConfiguring(DbContextOptionsBuilder optionsBuilder): Принимает объект DbContextOptionsBuilder в качестве параметра. DbContextOptionsBuilder используется для настройки различных параметров контекста базы данных.

Метод OnModelCreating - это переопределение метода в классе DbContext. Он вызывается Entity Framework Core, когда необходимо построить модель базы данных. Внутри этого метода используется объект ModelBuilder для настройки сопоставления между классами C# и таблицами базы данных.

modelBuilder.Entity<Article>(entity =>
{
    entity.HasKey(e => e.Id).HasName("article_pkey");

    entity.ToTable("article");

    entity.Property(e => e.Id)
        .HasColumnName("id_article")
        .ValueGeneratedOnAdd();
    entity.Property(e => e.ArticleBody)
        .HasMaxLength(1024)
        .HasColumnName("article_body");
    entity.Property(e => e.ArticleName)
        .HasMaxLength(128)
        .HasColumnName("article_name");
    entity.Property(e => e.IdUser).HasColumnName("id_user");

    entity.HasOne(d => d.IdUserNavigation).WithMany(p => p.Articles)
        .HasForeignKey(d => d.IdUser)
        .OnDelete(DeleteBehavior.ClientSetNull)
        .HasConstraintName("article_id_user_fkey");

    entity.HasMany(a => a.ArticleImages)
        .WithOne()
        .HasForeignKey(ai => ai.ArticleId)
        .OnDelete(DeleteBehavior.Cascade);
});

modelBuilder.Entity<Article>(entity => { ... });: Это отправная точка для настройки сущности Article. Все, что находится внутри фигурных скобок, относится к классу Article. Параметр entity — это лямбда-выражение, которое позволяет настроить сущность

entity.HasKey(e => e.Id).HasName("article_pkey");настраивает первичный ключ для сущности Article.

entity.HasKey(e => e.Id): Указывает, что свойство Id класса Article является первичным ключом. e => e.Id — это лямбда-выражение, которое выбирает свойство Id.
HasName("article_pkey"): Устанавливает имя ограничения первичного ключа в базе данных на “article_pkey”. 
entity.ToTable("article");: Указывает, что сущность Article отображается на таблицу с именем “article” в базе данных.

entity.Property(e => e.Id) ...: Настраивает свойства (столбцы) таблицы Article.

entity.Property(e => e.Id).HasColumnName("id_article").ValueGeneratedOnAdd();: Настраивает свойство Id:

HasColumnName("id_article"): Указывает, что столбец в базе данных, соответствующий свойству Id, называется “id_article”. Это полезно,чтобы имя столбца в базе данных отличалось от имени свойства в классе C#.

ValueGeneratedOnAdd(): Настраивает базу данных для автоматического создания значений для столбца Id при вставке новой строки (например, с использованием автоинкрементной последовательности).

entity.Property(e => e.ArticleBody).HasMaxLength(1024).HasColumnName("article_body");: Настраивает свойство ArticleBody:

HasMaxLength(1024): Указывает, что столбец article_body может хранить максимум 1024 символа. Это устанавливает ограничение длины в базе данных.

HasColumnName("article_body"): Указывает, что столбец базы данных называется “article_body”.

entity.Property(e => e.ArticleName).HasMaxLength(128).HasColumnName("article_name");: Аналогичная конфигурация для свойства ArticleName, ограничивающая его длину до 128 символов и сопоставляющая его со столбцом “article_name”.

entity.Property(e => e.IdUser).HasColumnName("id_user");: Настраивает свойство IdUser, сопоставляя его со столбцом “id_user”. Это свойство, вероятно, представляет внешний ключ, связывающий Article с User.

entity.HasOne(d => d.IdUserNavigation) ...: Настраивает отношение “один ко многим” между Article и User.

entity.HasOne(d => d.IdUserNavigation).WithMany(p => p.Articles): Определяет отношение.
HasOne(d => d.IdUserNavigation): Указывает, что Article имеет один связанный User. IdUserNavigation, вероятно, является навигационным свойством в классе Article, которое предоставляет доступ к связанному объекту User. (Навигационные свойства используются для обхода отношений между сущностями.)

WithMany(p => p.Articles): Указывает, что User может иметь много объектов Article. Articles является свойством коллекции (например, List<Article>) в классе User.
HasForeignKey(d => d.IdUser): Указывает, что свойство IdUser в классе Article является столбцом внешнего ключа, который связывается с таблицей User.
OnDelete(DeleteBehavior.ClientSetNull): Указывает поведение при удалении связанного User. ClientSetNull означает, что свойству IdUser в Article будет присвоено значение null, когда связанный User будет удален. Обрабатывается на стороне клиента.
HasConstraintName("article_id_user_fkey"): Устанавливает имя ограничения внешнего ключа в базе данных на “article_id_user_fkey”.
entity.HasMany(a => a.ArticleImages) ...: Настраивает отношение “один ко многим” между Article и ArticleImage.

entity.HasMany(a => a.ArticleImages): Указывает, что Article может иметь много связанных объектов ArticleImage.
WithOne(): Указывает, что ArticleImage имеет только один связанный Article.
HasForeignKey(ai => ai.ArticleId): Указывает, что свойство ArticleId в классе ArticleImage является столбцом внешнего ключа.
OnDelete(DeleteBehavior.Cascade): Указывает, что когда Article будет удален, все связанные объекты ArticleImage также будут удалены. Это известно как каскадное удаление.

Конфигурация сущности ArticleImage

modelBuilder.Entity<ArticleImage>(entity =>
{
    entity.HasKey(e => e.Id).HasName("article_images_pkey");

    entity.ToTable("article_images");

    entity.Property(e => e.Id)
        .ValueGeneratedOnAdd()
        .HasColumnName("id");
    entity.Property(e => e.ArticleId).HasColumnName("article_id");
    entity.Property(e => e.ImageUrl)
        .HasMaxLength(255)
        .HasColumnName("image_url");

    entity.HasOne(d => d.Article).WithMany(p => p.ArticleImages)
        .HasForeignKey(d => d.ArticleId)
        .HasConstraintName("article_images_article_id_fkey");
});

modelBuilder.Entity<ArticleImage>(entity => { ... });: Начинает конфигурацию для сущности ArticleImage.

entity.HasKey(e => e.Id).HasName("article_images_pkey");: Настраивает первичный ключ для сущности ArticleImage, именуя ограничение “article_images_pkey”.

entity.ToTable("article_images");: Указывает, что сущность ArticleImage отображается на таблицу “article_images”.

entity.Property(e => e.Id) ...: Настраивает свойства:

entity.Property(e => e.Id).ValueGeneratedOnAdd().HasColumnName("id");: Свойство Id является первичным ключом, и его значения автоматически генерируются базой данных. Имя столбца - “id”.
entity.Property(e => e.ArticleId).HasColumnName("article_id");: Свойство ArticleId сопоставляется со столбцом “article_id”. Это внешний ключ к таблице Article.
entity.Property(e => e.ImageUrl).HasMaxLength(255).HasColumnName("image_url");: Свойство ImageUrl сопоставляется со столбцом “image_url” с максимальной длиной 255 символов.
entity.HasOne(d => d.Article) ...: Настраивает отношение между ArticleImage и Article. Это обратное отношение, настроенное в сущности Article.

entity.HasOne(d => d.Article).WithMany(p => p.ArticleImages): HasOne указывает, что ArticleImage имеет один связанный Article. WithMany указывает, что Article может иметь много объектов ArticleImage (эта часть отражает отношение, определенное в конфигурации Article).

HasForeignKey(d => d.ArticleId): Указывает, что свойство ArticleId в классе ArticleImage является внешним ключом.
HasConstraintName("article_images_article_id_fkey"): Устанавливает имя ограничения внешнего ключа.

Конфигурация сущности Avatar

modelBuilder.Entity<Avatar>(entity =>
{
    entity.HasKey(e => e.Id).HasName("avatars_pkey");

    entity.ToTable("avatars");

    entity.Property(e => e.Id)
        .ValueGeneratedOnAdd()
        .HasColumnName("id");
    entity.Property(e => e.AvatarUrl)
        .HasMaxLength(255)
        .HasColumnName("avatar_url");
    entity.Property(e => e.DateAdded)
        .HasDefaultValueSql("CURRENT_TIMESTAMP")
        .HasColumnType("timestamp without time zone")
        .HasColumnName("date_added");
    entity.Property(e => e.IsActive)
        .HasDefaultValue(true)
        .HasColumnName("is_active");
    entity.Property(e => e.UserId).HasColumnName("user_id");

    entity.HasOne(d => d.User).WithMany(p => p.Avatars)
        .HasForeignKey(d => d.UserId)
        .HasConstraintName("avatars_user_id_fkey");
});

modelBuilder.Entity<Avatar>(entity => { ... });: Начинает конфигурацию для сущности Avatar.

entity.HasKey(e => e.Id).HasName("avatars_pkey");: Настраивает первичный ключ для сущности Avatar.

entity.ToTable("avatars");: Указывает, что сущность Avatar отображается на таблицу “avatars”.

entity.Property(e => e.Id) ...: Настраивает свойства:

entity.Property(e => e.Id).ValueGeneratedOnAdd().HasColumnName("id");: Свойство Id является первичным ключом, автоматически генерируется базой данных и сопоставляется со столбцом “id”.

entity.Property(e => e.AvatarUrl).HasMaxLength(255).HasColumnName("avatar_url");: Свойство AvatarUrl сопоставляется со столбцом “avatar_url” с максимальной длиной 255 символов.

entity.Property(e => e.DateAdded).HasDefaultValueSql("CURRENT_TIMESTAMP").HasColumnType("timestamp without time zone").HasColumnName("date_added");:

HasDefaultValueSql("CURRENT_TIMESTAMP"): Устанавливает значение по умолчанию для столбца date_added на текущую временную метку (используя функцию CURRENT_TIMESTAMP PostgreSQL). Это означает, что если явно не укажешь значение для DateAdded при вставке нового Avatar, база данных автоматически установит его на текущую дату и время.

HasColumnType("timestamp without time zone"): Указывает тип данных столбца date_added в базе данных. Хорошей практикой является явное определение типа данных, особенно при работе с базами данных.

HasColumnName("date_added"): Свойство DateAdded сопоставляется со столбцом “date_added”.

entity.Property(e => e.IsActive).HasDefaultValue(true).HasColumnName("is_active");: Свойство IsActive сопоставляется со столбцом “is_active” и имеет значение по умолчанию true. Это означает, что новые аватары будут активны по умолчанию.

entity.Property(e => e.UserId).HasColumnName("user_id");: Свойство UserId сопоставляется со столбцом “user_id”. Это внешний ключ к таблице User.
entity.HasOne(d => d.User) ...: Настраивает отношение между Avatar и User.

entity.HasOne(d => d.User).WithMany(p => p.Avatars): HasOne указывает, что Avatar имеет один связанный User. WithMany указывает, что User может иметь много объектов Avatar.
HasForeignKey(d => d.UserId): Указывает, что свойство UserId в классе Avatar является внешним ключом.
HasConstraintName("avatars_user_id_fkey"): Устанавливает имя ограничения внешнего ключа.

Конфигурация сущности ChatMember

modelBuilder.Entity<ChatMember>(e => {
    e.Property(c => c.ChatId).HasColumnName("chat_id");
    e.Property(c => c.UserId).HasColumnName("user_id");
    e.HasKey(gcm => new { gcm.ChatId, gcm.UserId });
    e.ToTable("chat_members");
    e.HasOne(gcm => gcm.Chat)
        .WithMany(g => g.Members)
        .HasForeignKey(gcm => gcm.ChatId);
});

modelBuilder.Entity<ChatMember>(e => { ... });: Начинает настройку для сущности ChatMember. Эта сущность представляет собой связующую таблицу между Chat и User, указывая, какие пользователи являются членами каких чатов.

e.Property(c => c.ChatId).HasColumnName("chat_id");: Настраивает свойство ChatId, сопоставляя его со столбцом “chat_id”.

e.Property(c => c.UserId).HasColumnName("user_id");: Настраивает свойство UserId, сопоставляя его со столбцом “user_id”.

e.HasKey(gcm => new { gcm.ChatId, gcm.UserId });: Настраивает составной первичный ключ, состоящий из ChatId и UserId. Это означает, что комбинация ChatId и UserId должна быть уникальной в таблице chat_members (пользователь может быть членом чата только один раз).

e.ToTable("chat_members");: Указывает, что сущность ChatMember сопоставляется с таблицей “chat_members”.

e.HasOne(gcm => gcm.Chat) ...: Настраивает отношение “один ко многим” между ChatMember и Chat. Это означает, что каждый ChatMember принадлежит одному конкретному Chat, и каждый Chat может иметь несколько записей ChatMember.

HasOne(gcm => gcm.Chat): Указывает, что ChatMember имеет один связанный Chat.
WithMany(g => g.Members): Указывает, что Chat может иметь много объектов ChatMember (представленных коллекцией Members в классе Chat).
HasForeignKey(gcm => gcm.ChatId): Указывает, что свойство ChatId в классе ChatMember является внешним ключом к таблице Chat.

Конфигурация сущности Chat

modelBuilder.Entity<Chat>(e => {
    e.Property(c => c.Id).HasColumnName("chat_id");
    e.Property(c => c.Name).HasColumnName("name");
    e.Property(c => c.OwnerId).HasColumnName("owner_id");
    e.Property(c => c.isDm).HasColumnName("is_dm");
    e.Property(c => c.CreatedAt).HasColumnName("created_at");
    e.ToTable("chats");
});

modelBuilder.Entity<Chat>(e => { ... });: Начинает настройку для сущности Chat. Эта сущность представляет собой чат-комнату или беседу.

e.Property(c => c.Id).HasColumnName("chat_id");: Настраивает свойство Id, сопоставляя его со столбцом “chat_id”.

e.Property(c => c.Name).HasColumnName("name");: Настраивает свойство Name, сопоставляя его со столбцом “name”.

e.Property(c => c.OwnerId).HasColumnName("owner_id");: Настраивает свойство OwnerId, сопоставляя его со столбцом “owner_id”. Это представляет пользователя, который создал или владеет чатом.

e.Property(c => c.isDm).HasColumnName("is_dm");: Настраивает свойство isDm, сопоставляя его со столбцом “is_dm”. Это, вероятно, указывает, является ли чат личным сообщением (один на один) или групповым чатом.

e.Property(c => c.CreatedAt).HasColumnName("created_at");: Настраивает свойство CreatedAt, сопоставляя его со столбцом “created_at”.

e.ToTable("chats");: Указывает, что сущность Chat сопоставляется с таблицей “chats”.

modelBuilder.Entity<User>(entity =>
{
    entity.HasKey(e => e.Id).HasName("customuser_pkey");

    entity.ToTable("customuser");

    entity.HasIndex(e => e.Name, "customuser_name_key");

    entity.Property(e => e.Id)
        .HasColumnName("id_user")
        .ValueGeneratedOnAdd();
    entity.Property(e => e.Description)
        .HasMaxLength(45)
        .HasColumnName("description");
    entity.Property(e => e.Email)
        .HasMaxLength(45)
        .HasColumnName("email");
    entity.Property(e => e.IdMbtiType).HasColumnName("id_mbti_type");
    entity.Property(e => e.IdRole).HasColumnName("id_role");
    entity.Property(e => e.LastLogin)
        .HasDefaultValueSql("CURRENT_TIMESTAMP")
        .HasColumnType("timestamp without time zone")
        .HasColumnName("last_login");
    entity.Property(e => e.Name)
        .HasMaxLength(45)
        .HasColumnName("name");

    entity.HasOne(d => d.IdMbtiTypeNavigation).WithMany(p => p.Customusers)
        .HasForeignKey(d => d.IdMbtiType)
        .OnDelete(DeleteBehavior.ClientSetNull)
        .HasConstraintName("customuser_id_mbti_type_fkey");

    entity.HasOne(d => d.IdRoleNavigation).WithMany(p => p.Customusers)
        .HasForeignKey(d => d.IdRole)
        .OnDelete(DeleteBehavior.ClientSetNull)
        .HasConstraintName("customuser_id_role_fkey");
});

modelBuilder.Entity<User>(entity => { ... });: Начинает настройку для сущности User.

entity.HasKey(e => e.Id).HasName("customuser_pkey");: Настраивает первичный ключ для сущности User, именуя ограничение “customuser_pkey”.

entity.ToTable("customuser");: Указывает, что сущность User сопоставляется с таблицей “customuser”. Имя таблицы customuser кажется немного необычным, возможно, лучше переименовать ее в более общепринятое Users.

entity.HasIndex(e => e.Name, "customuser_name_key");: Создает индекс для свойства Name, именуя его “customuser_name_key”. Это может повысить производительность запросов, которые выполняют поиск или сортировку по именам пользователей. Однако это не обеспечивает уникальность. Просто ускоряет поиск. Чтобы обеспечить уникальность, вам нужно было бы добавить .IsUnique().

entity.Property(e => e.Id) ...: Настраивает свойства:

entity.Property(e => e.Id).HasColumnName("id_user").ValueGeneratedOnAdd();: Свойство Id является первичным ключом, его значения автоматически генерируются базой данных, и оно сопоставляется со столбцом “id_user”.
entity.Property(e => e.Description).HasMaxLength(45).HasColumnName("description");: Свойство Description сопоставляется со столбцом “description”, с максимальной длиной 45 символов.
entity.Property(e => e.Email).HasMaxLength(45).HasColumnName("email");: Свойство Email сопоставляется со столбцом “email”, с максимальной длиной 45 символов.
entity.Property(e => e.IdMbtiType).HasColumnName("id_mbti_type");: Свойство IdMbtiType сопоставляется со столбцом “id_mbti_type”. Это, вероятно, внешний ключ к таблице MbtiType.
entity.Property(e => e.IdRole).HasColumnName("id_role");: Свойство IdRole сопоставляется со столбцом “id_role”. Это внешний ключ к таблице Role.
entity.Property(e => e.LastLogin).HasDefaultValueSql("CURRENT_TIMESTAMP").HasColumnType("timestamp without time zone").HasColumnName("last_login");: Свойство LastLogin сопоставляется со столбцом “last_login”, со значением по умолчанию, равным текущей временной метке, и типом данных timestamp без временной зоны.
entity.Property(e => e.Name).HasMaxLength(45).HasColumnName("name");: Свойство Name сопоставляется со столбцом “name”, с максимальной длиной 45 символов.
entity.HasOne(d => d.IdMbtiTypeNavigation) ...: Настраивает отношение между User и MbtiType.

HasOne(d => d.IdMbtiTypeNavigation).WithMany(p => p.Customusers): HasOne указывает, что User имеет один связанный MbtiType. WithMany указывает, что MbtiType может быть связан со многими объектами User (с использованием коллекции Customusers в классе MbtiType). 

HasForeignKey(d => d.IdMbtiType): Указывает, что свойство IdMbtiType в классе User является внешним ключом.

OnDelete(DeleteBehavior.ClientSetNull): Указывает поведение при удалении связанного MbtiType. ClientSetNull означает, что свойству IdMbtiType будет присвоено значение null на стороне клиента, что означает, что это должно обрабатываться приложением.

HasConstraintName("customuser_id_mbti_type_fkey"): Устанавливает имя ограничения внешнего ключа.
entity.HasOne(d => d.IdRoleNavigation) ...: Настраивает отношение между User и Role. Это аналогично отношению с MbtiType.

Конфигурация сущности Friend

modelBuilder.Entity<Friend>(entity =>
{
    //entity.HasKey(e => e.Id).HasName("friends_pkey");
    entity.HasKey(e => new { e.FriendId, e.UserId }).HasName("friends_pkey");

    entity.ToTable("friends");
    entity.Property(e => e.DateAdded)
        .HasDefaultValueSql("CURRENT_TIMESTAMP")
        .HasColumnType("timestamp without time zone")
        .HasColumnName("date_added");
    entity.Property(e => e.FriendId).HasColumnName("friend_id");
    entity.Property(e => e.UserId).HasColumnName("user_id");

    entity.HasOne(d => d.FriendNavigation).WithMany(p => p.FriendFriendNavigations)
        .HasForeignKey(d => d.FriendId)
        .HasConstraintName("friends_friend_id_fkey");

    entity.HasOne(d => d.User).WithMany(p => p.FriendUsers)
        .HasForeignKey(d => d.UserId)
        .HasConstraintName("friends_user_id_fkey");
});

modelBuilder.Entity<Friend>(entity => { ... });: Начинает настройку для сущности Friend. Эта сущность представляет связь между двумя пользователями (которые являются друзьями).

//entity.HasKey(e => e.Id).HasName("friends_pkey");: Эта строка закомментирована, предполагая, что изначально у сущности Friend мог быть один Id в качестве первичного ключа.

entity.HasKey(e => new { e.FriendId, e.UserId }).HasName("friends_pkey");: Настраивает составной первичный ключ, состоящий из FriendId и UserId. Это очень важно, потому что указывает, что дружба определяется комбинацией двух идентификаторов пользователей. Отношения дружбы между одними и теми же двумя пользователями не могут существовать несколько раз.

entity.ToTable("friends");: Указывает, что сущность Friend сопоставляется с таблицей “friends”.

entity.Property(e => e.DateAdded) ...: Настраивает свойство DateAdded:

HasDefaultValueSql("CURRENT_TIMESTAMP"): Устанавливает значение по умолчанию равным текущей временной метке.

HasColumnType("timestamp without time zone"): Указывает тип данных как “timestamp без временной зоны”.

HasColumnName("date_added"): Сопоставляет свойство со столбцом “date_added”.

entity.Property(e => e.FriendId).HasColumnName("friend_id");: Настраивает свойство FriendId, сопоставляя его со столбцом “friend_id”. Это представляет идентификатор пользователя, с которым дружат.

entity.Property(e => e.UserId).HasColumnName("user_id");: Настраивает свойство UserId, сопоставляя его со столбцом “user_id”. Это, вероятно, представляет идентификатор пользователя, инициировавшего дружбу.

entity.HasOne(d => d.FriendNavigation) ...: Настраивает отношение, в котором FriendId является внешним ключом, ссылающимся на таблицу User (представляя пользователя, с которым дружат):

HasOne(d => d.FriendNavigation).WithMany(p => p.FriendFriendNavigations): Указывает на отношение, в котором Friend имеет одного связанного User (доступ через FriendNavigation), и User может быть “другом” во множестве отношений Friend (доступ через коллекцию FriendFriendNavigations).

HasForeignKey(d => d.FriendId): Указывает, что FriendId является внешним ключом.

HasConstraintName("friends_friend_id_fkey"): Именует ограничение внешнего ключа.

entity.HasOne(d => d.User) ...: Настраивает отношение, в котором UserId является внешним ключом, ссылающимся на таблицу User (представляя пользователя, инициировавшего дружбу):

HasOne(d => d.User).WithMany(p => p.FriendUsers): Указывает на отношение, в котором Friend имеет одного связанного User (доступ через User), и User может инициировать множество отношений Friend 

HasForeignKey(d => d.UserId): Указывает, что UserId является внешним ключом.

HasConstraintName("friends_user_id_fkey"): Именует ограничение внешнего ключа.

modelBuilder.Entity<Like>(entity =>
{
    entity
        .HasNoKey()
        .ToTable("likes");
    entity.Property(e => e.LikedAt)
        .HasDefaultValueSql("CURRENT_TIMESTAMP")
        .HasColumnType("timestamp without time zone")
        .HasColumnName("liked_at");
    entity.Property(e => e.PostId).HasColumnName("post_id");
    entity.Property(e => e.UserId).HasColumnName("user_id");

    entity.HasOne(d => d.Post).WithMany()
        .HasForeignKey(d => d.PostId)
        .OnDelete(DeleteBehavior.Cascade)
        .HasConstraintName("likes_post_id_fkey");

    entity.HasOne(d => d.User).WithMany()
        .HasForeignKey(d => d.UserId)
        .OnDelete(DeleteBehavior.Cascade)
        .HasConstraintName("likes_user_id_fkey");
});

modelBuilder.Entity<Like>(entity => { ... });: Начинает настройку для сущности Like. Эта сущность представляет собой то, что пользователю нравится пост.

entity.HasNoKey():  Это указывает на то, что у сущности Like нет первичного ключа. Это разрешено в EF Core, но это означает, что не получится отслеживать экземпляры сущности Like непосредственно с помощью отслеживания изменений EF Core.

entity.ToTable("likes");: Указывает, что сущность Like сопоставляется с таблицей “likes”.

entity.Property(e => e.LikedAt) ...: Настраивает свойство LikedAt:

HasDefaultValueSql("CURRENT_TIMESTAMP"): Устанавливает значение по умолчанию равным текущей временной метке.

HasColumnType("timestamp without time zone"): Указывает тип данных.

HasColumnName("liked_at"): Сопоставляет свойство со столбцом “liked_at”.

entity.Property(e => e.PostId).HasColumnName("post_id");: Настраивает свойство PostId, сопоставляя его со столбцом “post_id”.

entity.Property(e => e.UserId).HasColumnName("user_id");: Настраивает свойство UserId, сопоставляя его со столбцом “user_id”.

entity.HasOne(d => d.Post) ...: Настраивает отношение между Like и Post.

HasOne(d => d.Post).WithMany(): Указывает, что Like принадлежит одному Post, и Post может иметь много объектов Like (WithMany() указывает, что в сущности Post нет явного свойства навигации по коллекции).

HasForeignKey(d => d.PostId): Указывает, что PostId является внешним ключом.

OnDelete(DeleteBehavior.Cascade): Указывает, что при удалении Post также удаляются все связанные записи Like.
HasConstraintName("likes_post_id_fkey"): Именует ограничение внешнего ключа.

entity.HasOne(d => d.User) ...: Настраивает отношение между Like и User.

HasOne(d => d.User).WithMany(): Указывает, что Like принадлежит одному User, и User может иметь много объектов Like (опять же, в сущности User не определено свойство навигации).

HasForeignKey(d => d.UserId): Указывает, что UserId является внешним ключом.

OnDelete(DeleteBehavior.Cascade): Указывает, что при удалении User также удаляются все связанные записи Like.
HasConstraintName("likes_user_id_fkey"): Именует ограничение внешнего ключа.

Все остальное аналогично:


        modelBuilder.Entity<MbtiType>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("mbti_type_pkey");

            entity.ToTable("mbti_type");

            entity.Property(e => e.Id)
            .ValueGeneratedOnAdd()
            .HasColumnName("id_mbti_type");
            entity.Property(e => e.NameOfType)
                .HasMaxLength(45)
                .HasColumnName("name_of_type");
            entity.HasData(
                new MbtiType[]
                {
                    new MbtiType{Id = 1, NameOfType ="ENFJ"},
                    new MbtiType{Id = 2, NameOfType ="ENTJ"},
                    new MbtiType{Id = 3, NameOfType ="ENFP"},
                    new MbtiType{Id = 4, NameOfType ="ENTP"},
                    new MbtiType{Id = 5, NameOfType ="INFJ"},
                    new MbtiType{Id = 6, NameOfType ="INTJ"},
                    new MbtiType{Id = 7, NameOfType ="INFP"},
                    new MbtiType{Id = 8, NameOfType ="INTP"},
                    new MbtiType{Id = 9, NameOfType ="ISFP"},
                    new MbtiType{Id = 10, NameOfType ="ISFJ"},
                    new MbtiType{Id = 11, NameOfType ="ESFP"},
                    new MbtiType{Id = 12, NameOfType ="ESFJ"},
                    new MbtiType{Id = 13, NameOfType ="ISTJ"},
                    new MbtiType{Id = 14, NameOfType ="ISTP"},
                    new MbtiType{Id = 15, NameOfType ="ESTP"},
                    new MbtiType{Id = 16, NameOfType ="ESTJ"},
                }
                );
        });
        modelBuilder.Entity<Message>(e => {
            e.Property(m => m.MessageId).HasColumnName("message_id");
            e.Property(m => m.ChatId).HasColumnName("chat_id");
            e.Property(m => m.SenderId).HasColumnName("sender_id");
            e.Property(m => m.Msg).HasColumnName("msg");
            e.Property(m => m.Date).HasColumnName("date");
            e.Property(m => m.IsEdited).HasColumnName("is_edited");
            e.Property(m => m.IsFunctional).HasColumnName("is_functional");
            // e.Property(m => m.Attachments).HasColumnName("attachments");
            // e.Property(m => m.MessageStatuses).HasColumnName("message_statuses");
            e.ToTable("messages");
            e.Property(m => m.MessageId)
                .ValueGeneratedOnAdd()
                .HasAnnotation("Npgsql:ValueGenerationStrategy", NpgsqlValueGenerationStrategy.SerialColumn);
        });
            
        modelBuilder.Entity<MessageStatus>()
            .ToTable("message_statuses")
            .HasKey(ms => new { ms.UserId, ms.ChatId });

        modelBuilder.Entity<MessageStatus>(e => {
            e.Property(ms => ms.UserId).HasColumnName("user_id");
            e.Property(ms => ms.ChatId).HasColumnName("chat_id");
            e.Property(ms => ms.LastReaden).HasColumnName("last_readen");
            e.ToTable("message_statuses");
            e.HasOne<Chat>()
                .WithMany()
                .HasForeignKey(ms => ms.ChatId)
                .OnDelete(DeleteBehavior.Cascade);
        });
            
        
        modelBuilder.Entity<Post>(entity =>
        {
        entity.HasKey(e => e.Id).HasName("post_pkey");

        entity.ToTable("post");
            entity.Property(e => e.Id)
                        .ValueGeneratedOnAdd()
                        .HasColumnName("id_post");
            entity.Property(e => e.IdUser).HasColumnName("id_user");
            entity.Property(e => e.PostBody)
                .HasMaxLength(1024)
                .HasColumnName("post_body");

            entity.HasOne(d => d.IdUserNavigation).WithMany(p => p.Posts)
                .HasForeignKey(d => d.IdUser)
                .OnDelete(DeleteBehavior.ClientSetNull)
                .HasConstraintName("post_id_user_fkey");
        });

        modelBuilder.Entity<PostImage>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("post_images_pkey");

            entity.ToTable("post_images");

            entity.Property(e => e.Id)
            .ValueGeneratedOnAdd()
            .HasColumnName("id");
            entity.Property(e => e.ImageUrl)
                .HasMaxLength(255)
                .HasColumnName("image_url");
            entity.Property(e => e.PostId).HasColumnName("post_id");

            entity.HasOne(d => d.Post).WithMany(p => p.PostImages)
                .HasForeignKey(d => d.PostId)
                .HasConstraintName("post_images_post_id_fkey");
        });

        modelBuilder.Entity<Role>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("role_pkey");

            entity.ToTable("role");

            entity.Property(e => e.Id)
            .ValueGeneratedOnAdd()
            .HasColumnName("id_role");
            entity.Property(e => e.NameRole)
                .HasMaxLength(45)
                .HasColumnName("name_role");
            entity.HasData(
                new Role[]
                {
                    new Role{ Id = 1, NameRole = "User"},
                    new Role{ Id = 2, NameRole = "Admin"},
                    new Role{ Id = 3, NameRole = "Moderator"}
                }
                );
        });

        modelBuilder.Entity<Tag>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("tag_pkey");

            entity.ToTable("tag");

            entity.Property(e => e.Id)
            .ValueGeneratedOnAdd()
            .HasColumnName("id_tag");
            entity.Property(e => e.NameTag)
                .HasMaxLength(45)
                .HasColumnName("name_tag");
        });

        modelBuilder.Entity<UserTag>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("user_tags_pkey");

            entity.ToTable("user_tags");

            entity.Property(e => e.Id)
            .ValueGeneratedOnAdd()
            .HasColumnName("id_user_tags");
            entity.Property(e => e.IdTag).HasColumnName("id_tag");
            entity.Property(e => e.IdUser).HasColumnName("id_user");

            entity.HasOne(d => d.IdTagNavigation).WithMany(p => p.UserTags)
                .HasForeignKey(d => d.IdTag)
                .OnDelete(DeleteBehavior.ClientSetNull)
                .HasConstraintName("user_tags_id_tag_fkey");

            entity.HasOne(d => d.IdUserNavigation).WithMany(p => p.UserTags)
                .HasForeignKey(d => d.IdUser)
                .OnDelete(DeleteBehavior.ClientSetNull)
                .HasConstraintName("user_tags_id_user_fkey");
        });

        OnModelCreatingPartial(modelBuilder);
    

    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);


