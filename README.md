### Выполнение дипломной работы "Разработка приложения на C#"

#### Подготовительный этап
1. **Определение цели и задач проекта:**
   - Цель: разработка приложения для управления записями в поликлинику.
   - Задачи: анализ требований, проектирование системы, разработка и тестирование, внедрение и документация.

2. **Сбор требований:**
   - Обсудите с заказчиком функциональные требования (возможности и ограничения).
   - Определите нефункциональные требования (производительность, безопасность, масштабируемость).

3. **Выбор инструментов и технологий:**
   - Язык программирования: C#.
   - Среда разработки: Visual Studio.
   - Фреймворк для создания API: ASP.NET Core.
   - Система управления базами данных: SQL Server.
   - Инструменты для документирования: Microsoft Word, Visio для диаграмм.

#### Модуль 1: Осуществление интеграции программных модулей
1. **Диаграмма прецедентов (Use Case):**
   - Определите основных пользователей системы (пациенты, врачи, администраторы).
   - Создайте диаграмму, показывающую взаимодействие пользователей с системой.
   - Используйте Microsoft Visio или аналогичный инструмент.
   - Пример:

     ```plaintext
     Актёры: Пациент, Врач, Администратор
     Прецеденты: Регистрация, Запись на приём, Просмотр расписания, Подтверждение приёма
     ```

2. **Диаграмма деятельности (Activity Diagram):**
   - Опишите последовательность действий для основных процессов.
   - Пример: процесс записи на приём.
   - Создайте диаграмму в Visio и экспортируйте в PDF.

3. **Диаграмма последовательности (Sequence Diagram):**
   - Опишите взаимодействие объектов (например, пользователь и система) для основного процесса.
   - Пример: взаимодействие для процесса записи на приём.
   - Создайте диаграмму и экспортируйте в PDF.

4. **Разработка тестовых наборов:**
   - Определите тестовые сценарии для проверки функциональности.
   - Пример: тестирование правильности даты приёма.
   - Создайте документ в Word с тестовыми данными и экспортируйте в PDF.

5. **Руководство пользователя:**
   - Опишите пошаговые инструкции для работы с приложением.
   - Включите скриншоты для наглядности.
   - Оформите документ с титульным листом и оглавлением.

#### Модуль 2: Разработка, администрирование и защита баз данных
1. **Проектирование и разработка объектов баз данных:**
   - Спроектируйте ER-диаграмму базы данных.
   - Пример:

     ```plaintext
     Таблицы: Пациенты, Врачи, Записи
     Связи: Пациенты (1:N) Записи, Врачи (1:N) Записи
     ```

   - Реализуйте таблицы и связи в SQL Server.
   - Добавьте начальные данные.

2. **Процедура проверки данных:**
   - Напишите процедуру для проверки корректности даты приёма.
   - Пример процедуры на T-SQL:

     ```sql
     CREATE PROCEDURE CheckAppointmentDate
     @AppointmentDate DATE
     AS
     BEGIN
         IF @AppointmentDate < GETDATE()
         BEGIN
             PRINT 'Invalid Date';
         END
         ELSE
         BEGIN
             PRINT 'Valid Date';
         END
     END
     ```

3. **Создание триггера:**
   - Напишите триггер для логирования изменений.
   - Пример:

     ```sql
     CREATE TRIGGER trgAfterUpdateOnAppointments
     ON Appointments
     AFTER UPDATE
     AS
     BEGIN
         INSERT INTO AppointmentLog (AppointmentId, OldDate, NewDate, ChangeDate)
         SELECT i.Id, d.Date, i.Date, GETDATE()
         FROM inserted i
         JOIN deleted d ON i.Id = d.Id;
     END
     ```

4. **Резервное копирование:**
   - Напишите скрипт для создания бэкапа базы данных.
   - Пример:

     ```sql
     BACKUP DATABASE ClinicDB
     TO DISK = 'C:\Backups\ClinicDB.bak'
     WITH FORMAT;
     ```

#### Модуль 3: Разработка модулей программного обеспечения для компьютерных систем
1. **Разработка API:**
   - Создайте ASP.NET Core проект в Visual Studio.
   - Реализуйте контроллеры для управления данными (пациенты, записи на приём).
   - Пример контроллера:

     ```csharp
     [ApiController]
     [Route("api/[controller]")]
     public class PatientsController : ControllerBase
     {
         private readonly ApplicationDbContext _context;

         public PatientsController(ApplicationDbContext context)
         {
             _context = context;
         }

         [HttpGet]
         public async Task<ActionResult<IEnumerable<Patient>>> GetPatients()
         {
             return await _context.Patients.ToListAsync();
         }

         [HttpPost]
         public async Task<ActionResult<Patient>> CreatePatient(Patient patient)
         {
             _context.Patients.Add(patient);
             await _context.SaveChangesAsync();
             return CreatedAtAction(nameof(GetPatient), new { id = patient.Id }, patient);
         }

         // Другие методы...
     }
     ```

2. **Документация API:**
   - Используйте Swagger для автоматической генерации документации API.
   - Настройте Swagger в проекте ASP.NET Core:

     ```csharp
     public void ConfigureServices(IServiceCollection services)
     {
         services.AddControllers();
         services.AddSwaggerGen(c =>
         {
             c.SwaggerDoc("v1", new OpenApiInfo { Title = "Clinic API", Version = "v1" });
         });
     }

     public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
     {
         if (env.IsDevelopment())
         {
             app.UseDeveloperExceptionPage();
         }

         app.UseSwagger();
         app.UseSwaggerUI(c =>
         {
             c.SwaggerEndpoint("/swagger/v1/swagger.json", "Clinic API v1");
         });

         app.UseRouting();
         app.UseAuthorization();
         app.UseEndpoints(endpoints =>
         {
             endpoints.MapControllers();
         });
     }
     ```

#### Заключительный этап
1. **Тестирование:**
   - Проведите тестирование всех функциональных модулей.
   - Зафиксируйте результаты тестов в документе.

2. **Документирование:**
   - Соберите всю документацию (руководство пользователя, документация API, результаты тестирования).
   - Убедитесь в правильности и полноте документации.

3. **Презентация проекта:**
   - Подготовьте презентацию для защиты дипломной работы.
   - Включите ключевые моменты проекта, демонстрацию работы приложения и ответы на вопросы.

Выполнение этих шагов поможет вам успешно разработать и представить дипломную работу по теме "Разработка приложения на C#".
