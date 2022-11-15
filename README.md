# Day8Notes
# Day8

# Asynchronous calls:

 asynchronous calls, the response to the API call is returned immediately with a polling URL while the request continues to be processed.

Synchronous calls: Synchronous API calls are **blocking calls that do not return until either the change has been completed or there has been an error**

### **What Is A Task In C#?**

A task in C# is used to implement Task-based Asynchronous Programming and was introduced with the .NET Framework 4. The Task object is typically executed asynchronously on a thread pool thread rather than synchronously on the main thread of the application.

### **What is a Thread pool in C#?**

A **Thread pool in C#** is a collection of **threads** that can be used to perform a number of tasks in the background. Once a thread completes its task, then again it is sent to the thread pool, so that it can be reused. This reusability of threads avoids an application to create a number of threads which ultimately uses less memory consumption.

### **Why do we need to use a Task In C#?**

Tasks in C# basically used to make your application more responsive. If the thread that manages the user interface offloads the works to other threads from the thread pool, then it can keep processing user events which will ensure that the application can still be used.

1. Lets Go to the ICharacgterService.cs and add the Task 
2. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Services.CharacterService
{
    public interface ICharacterService
    {
        Task<List<Character>> GetAllCharacters();

        Task<Character> GetCharacterById(int id);

        Task<List<Character>> AddCharacter(Character newCharacter);
    }
}
```

1. No we go to the CharacterService.cs
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace dotnet_rpg.Services.CharacterService
    {
        public class CharacterService : ICharacterService
        {
    
            private static List<Character> characters = new List<Character>{
                new Character(),
                new Character { Id = 1,Name = "sam"}
            };
            public Task<List<Character>> AddCharacter(Character newCharacter)
            {
               characters.Add(newCharacter); 
               return characters;
            }
    
            public Task<List<Character>> GetAllCharacters()
            {
              return characters;
            }
    
            public Task<Character> GetCharacterById(int id)
            {
                return characters.FirstOrDefault(c => c.Id == id);
            }
        }
    }
    ```
    
    1. Now add the async to all the methods
        1. 
        
        ```csharp
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Threading.Tasks;
        
        namespace dotnet_rpg.Services.CharacterService
        {
            public class CharacterService : ICharacterService
            {
        
                private static List<Character> characters = new List<Character>{
                    new Character(),
                    new Character { Id = 1,Name = "sam"}
                };
                public async Task<List<Character>> AddCharacter(Character newCharacter)
                {
                   characters.Add(newCharacter); 
                   return characters;
                }
        
                public async Task<List<Character>> GetAllCharacters()
                {
                  return characters;
                }
        
                public async Task<Character> GetCharacterById(int id)
                {
                    return characters.FirstOrDefault(c => c.Id == id);
                }
            }
        }
        ```
        
        1. Lets go to the CharacterController.cs
            1. We will add the Task, async and await
                1. 
                
                ```csharp
                using System;
                using System.Collections.Generic;
                using System.Linq;
                using System.Threading.Tasks;
                using dotnet_rpg.Services.CharacterService;
                using Microsoft.AspNetCore.Mvc;
                
                namespace dotnet_rpg.Controllers
                {
                
                    [ApiController]
                    [Route("api/[controller]")]
                    public class CharacterController : ControllerBase
                    {
                        private readonly ICharacterService characterService;
                        
                        public CharacterController(ICharacterService characterService)
                        {
                            this.characterService = characterService;
                            
                        }
                        
                        [HttpGet("GetAll")]
                        public async Task<ActionResult<List<Character>>> Get()
                        {
                            return Ok(await this.characterService.GetAllCharacters());
                        }
                
                        [HttpGet("{id}")]
                        public async Task<ActionResult<Character>> GetSingle(int id)
                        {
                            return Ok(await this.characterService.GetCharacterById(id));
                        }
                
                         [HttpPost]   
                        public async Task<ActionResult<List<Character>>> AddCharacter(Character newCharacter)
                        {
                            
                            return Ok(await this.characterService.AddCharacter(newCharacter));
                        }
                    }
                }
                ```
                
            
            6.  Let test it on Swagger
            
            1. In the VS code Terminal run : dotnet watch run
                1. It all should work the same.
            
    
    # Proper Service Response with Generics
    
    1. 
    
    # What is a Wrapper Class?
    
    The main idea of a wrapper class is to mimic the functionality of another class while hiding the mimicked class' complexity. A wrapper is a class that takes an instance of another class (the class being wrapped) as an argument in its constructor, making it a primitive class of the one being wrapped. This means the wrapper has access to the wrapped class' functionality and is able to expose the desired features/build utility components on top of this wrapped class.
    
    More on Nullable : [https://www.geeksforgeeks.org/c-sharp-nullable-types/#:~:text=In C%23%2C the compiler does,null value to a variable](https://www.geeksforgeeks.org/c-sharp-nullable-types/#:~:text=In%20C%23%2C%20the%20compiler%20does,null%20value%20to%20a%20variable).
    
    1. Lets create a new file under our Modles Folder:
        1. Create a new class and name it : ServiceResponse
        2. Now we have ServiceResponse.cs
            1. The actual class is ServiceResponse<T>
            2. 
            
            ```csharp
            using System;
            using System.Collections.Generic;
            using System.Linq;
            using System.Threading.Tasks;
            
            namespace dotnet_rpg.Models
            {
                public class ServiceResponse<T>
                {
                    public T? Data { get; set; }
            
                    public bool Success { get; set; } = true;
            
                    public string Message { get; set; } = string.Empty;
                }
            }
            ```
            
            1. Now we need to modify ICharacterService.cs
                1. We add the ServiceResponse
                    1. 
                    
                    ```csharp
                    using System;
                    using System.Collections.Generic;
                    using System.Linq;
                    using System.Threading.Tasks;
                    
                    namespace dotnet_rpg.Services.CharacterService
                    {
                        public interface ICharacterService
                        {
                            Task<ServiceResponse<List<Character>>> GetAllCharacters();
                    
                            Task<ServiceResponse<Character>> GetCharacterById(int id);
                    
                            Task<ServiceResponse<List<Character>>> AddCharacter(Character newCharacter);
                        }
                    }
                    ```
                    
                    1. Now we do that with CharacterService.cs
                        1. Lets add the ServiceResponse and proper code
                            1. 
                            
                            ```csharp
                            using System;
                            using System.Collections.Generic;
                            using System.Linq;
                            using System.Threading.Tasks;
                            
                            namespace dotnet_rpg.Services.CharacterService
                            {
                                public class CharacterService : ICharacterService
                                {
                            
                                    private static List<Character> characters = new List<Character>{
                                        new Character(),
                                        new Character { Id = 1,Name = "sam"}
                                    };
                                    public async Task<ServiceResponse<List<Character>>> AddCharacter(Character newCharacter)
                                    {
                                       var serviceResponse = new ServiceResponse<List<Character>>();
                                    
                                       characters.Add(newCharacter);
                                       serviceResponse.Data = characters; 
                                       return serviceResponse;
                                    }
                            
                                    public async Task<ServiceResponse<List<Character>>> GetAllCharacters()
                                    {
                                      return new ServiceResponse<List<Character>> { Data = characters};
                                    }
                            
                                    public async Task<ServiceResponse<Character>> GetCharacterById(int id)
                                    {
                                        var serviceResponse = new ServiceResponse<Character>();
                                        var character = characters.FirstOrDefault(c => c.Id == id);
                                        return serviceResponse;
                                    }
                                }
                            }
                            ```
                            
                            1. Lets go to our CharacterController.cs
                                1. add the service response as return type of the methods of the CharacterController.cs so that we see the proper changes in Swagger UI.
                                2. 
                                
                                ```csharp
                                using System;
                                using System.Collections.Generic;
                                using System.Linq;
                                using System.Threading.Tasks;
                                using dotnet_rpg.Services.CharacterService;
                                using Microsoft.AspNetCore.Mvc;
                                
                                namespace dotnet_rpg.Controllers
                                {
                                
                                    [ApiController]
                                    [Route("api/[controller]")]
                                    public class CharacterController : ControllerBase
                                    {
                                        private readonly ICharacterService characterService;
                                        
                                        public CharacterController(ICharacterService characterService)
                                        {
                                            this.characterService = characterService;
                                            
                                        }
                                        
                                        [HttpGet("GetAll")]
                                        public async Task<ActionResult<ServiceResponse<List<Character>>>> Get()
                                        {
                                            return Ok(await this.characterService.GetAllCharacters());
                                        }
                                
                                        [HttpGet("{id}")]
                                        public async Task<ActionResult<ServiceResponse<Character>>> GetSingle(int id)
                                        {
                                            return Ok(await this.characterService.GetCharacterById(id));
                                        }
                                
                                         [HttpPost]   
                                        public async Task<ActionResult<ServiceResponse<List<Character>>>> AddCharacter(Character newCharacter)
                                        {
                                            
                                            return Ok(await this.characterService.AddCharacter(newCharacter));
                                        }
                                    }
                                }
                                ```
                                
                                1. Now lets take a look at swagger
                                    1. dotnet watch run
                                        1. go over shema
                                            1. now you have CharacterLisServiceResponse
                                                1. CharacterServiceResponse
                                        2. In the GetAll GET you see the data dsoplayed diffrently with our Wrapped messgea “” and success true, data.
                                        3. 
                                        
                                        ```json
                                        {
                                          "data": [
                                            {
                                              "id": 0,
                                              "name": "Frodo",
                                              "hitPoints": 100,
                                              "strength": 10,
                                              "defense": 10,
                                              "intelligence": 10,
                                              "class": "Knight"
                                            },
                                            {
                                              "id": 1,
                                              "name": "sam",
                                              "hitPoints": 100,
                                              "strength": 10,
                                              "defense": 10,
                                              "intelligence": 10,
                                              "class": "Knight"
                                            }
                                          ],
                                          "success": true,
                                          "message": ""
                                        }
                                        ```
                                        
                                
                                # DATA TRANSFER OBJECTS (DTO’S)
                                
                                For all he data transfer objects regarding the RPG characters
                                
                                When you have smaller objects that do not consist of eery property of the corresponding model.  When we create a database table for our RPG characters later in this course, we could add properties like the created and modified date .
                                
                                We don’t want to send this data to the client, so we map certain properties of the model to the DTO
                                
                                1. Lets Create a Folder in the root called Dtos
                                    1. In that folder create another folder and name that one : Character
                                        1. Inside the Character Folder
                                            1. Create a new file class name it GetCharacterDto
                                                1. Now in the GetCharacterDto.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Dtos.Character
{
    public class GetCharacterDto
    {
        
    }
}
```

1. Add another Class name it : AddCharacterDto
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace dotnet_rpg.Dtos.Character
    {
        public class AddCharacterDto
        {
            
        }
    }
    ```
    
2. Go to the Models folder under Character.cs
    1. Copy and paste the following
    2. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace dotnet_rpg.Models
    {
        public class Character
        {
            public int Id { get; set; }
    
            public string Name { get; set; } = "Frodo";
    
            public int HitPoints { get; set; } = 100;
    
            public int Strength { get; set; } = 10;
    
            public int Defense { get; set; } = 10;
    
            public int Intelligence { get; set; } = 10;
    
            public RpgClass Class { get; set; } = RpgClass.Knight;
        }
    }
    ```
    
    1. Go to the GetCharacterDto.cs and pate it
        1. 
        
        ```csharp
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Threading.Tasks;
        
        namespace dotnet_rpg.Dtos.Character
        {
            public class GetCharacterDto
            {
                   public int Id { get; set; }
        
                public string Name { get; set; } = "Frodo";
        
                public int HitPoints { get; set; } = 100;
        
                public int Strength { get; set; } = 10;
        
                public int Defense { get; set; } = 10;
        
                public int Intelligence { get; set; } = 10;
        
                public RpgClass Class { get; set; } = RpgClass.Knight;
            }
        }
        ```
        
        1. Do the same with the AddCharacterDto.cs but remove the Id get set.
            1. 
            
            ```csharp
            using System;
            using System.Collections.Generic;
            using System.Linq;
            using System.Threading.Tasks;
            
            namespace dotnet_rpg.Dtos.Character
            {
                public class AddCharacterDto
                {
                  
            
                    public string Name { get; set; } = "Frodo";
            
                    public int HitPoints { get; set; } = 100;
            
                    public int Strength { get; set; } = 10;
            
                    public int Defense { get; set; } = 10;
            
                    public int Intelligence { get; set; } = 10;
            
                    public RpgClass Class { get; set; } = RpgClass.Knight;
                }
            }
            ```
            
        2. Go to the ICharacterService.cs and add replace the Character with GetCharacterDto
            1. 
            
            ```csharp
            using System;
            using System.Collections.Generic;
            using System.Linq;
            using System.Threading.Tasks;
            
            namespace dotnet_rpg.Services.CharacterService
            {
                public interface ICharacterService
                {
                    Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters();
            
                    Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id);
            
                    Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter);
                }
            }
            ```
            
        3. Same with CharacterService.cs
            1. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Services.CharacterService
{
    public class CharacterService : ICharacterService
    {

        private static List<Character> characters = new List<Character>{
            new Character(),
            new Character { Id = 1,Name = "sam"}
        };
        public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
        {
           var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
        
           characters.Add(newCharacter);
           serviceResponse.Data = characters; 
           return serviceResponse;
        }

        public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
        {
          return new ServiceResponse<List<GetCharacterDto>> { Data = characters};
        }

        public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
        {
            var serviceResponse = new ServiceResponse<GetCharacterDto>();
            var character = characters.FirstOrDefault(c => c.Id == id);
            serviceResponse.Data = character;
            return serviceResponse;
        }
    }
}
```

1. Go to the CharacterController.cs and add the CharacterDto
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using dotnet_rpg.Services.CharacterService;
    using Microsoft.AspNetCore.Mvc;
    
    namespace dotnet_rpg.Controllers
    {
    
        [ApiController]
        [Route("api/[controller]")]
        public class CharacterController : ControllerBase
        {
            private readonly ICharacterService characterService;
            
            public CharacterController(ICharacterService characterService)
            {
                this.characterService = characterService;
                
            }
            
            [HttpGet("GetAll")]
            public async Task<ActionResult<ServiceResponse<List<GetCharacterDto>>> Get()
            {
                return Ok(await this.characterService.GetAllCharacters());
            }
    
            [HttpGet("{id}")]
            public async Task<ActionResult<ServiceResponse<GetCharacterDto>>> GetSingle(int id)
            {
                return Ok(await this.characterService.GetCharacterById(id));
            }
    
             [HttpPost]   
            public async Task<ActionResult<ServiceResponse<List<GetCharacterDto>>>> AddCharacter(AddCharacterDto newCharacter)
            {
                
                return Ok(await this.characterService.AddCharacter(newCharacter));
            }
        }
    }
    ```
    

# AUTOMAPPER

1. Lets install auto mapper
    1. [https://www.nuget.org/packages/AutoMapper.Extensions.Microsoft.DependencyInjection](https://www.nuget.org/packages/AutoMapper.Extensions.Microsoft.DependencyInjection)
        1. run this command in the CLI: dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection 
    2. Now we need to register automapper in our Program.cs file
        1. 
        
        ```csharp
        global using dotnet_rpg.Models;
        using dotnet_rpg.Services.CharacterService;
        
        var builder = WebApplication.CreateBuilder(args);
        
        // Add services to the container.
        
        builder.Services.AddControllers();
        // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
        builder.Services.AddEndpointsApiExplorer();
        builder.Services.AddSwaggerGen();
        builder.Services.AddAutoMapper(typeof(Program).Assembly);
        builder.Services.AddScoped<ICharacterService, CharacterService>();
        
        var app = builder.Build();
        
        // Configure the HTTP request pipeline.
        if (app.Environment.IsDevelopment())
        {
            app.UseSwagger();
            app.UseSwaggerUI();
        }
        
        app.UseHttpsRedirection();
        
        app.UseAuthorization();
        
        app.MapControllers();
        
        app.Run();
        ```
        
    
    2.
