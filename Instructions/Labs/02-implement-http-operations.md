---
lab:
  title: "Ejercicio: Implementación de operaciones HTTP en Razor\_Pages de ASP.NET\_Core"
  module: 'Module: Implement HTTP operations in ASP.NET Core Razor Pages'
---

En este ejercicio, aprenderás a agregar código a una aplicación Razor Pages de ASP.NET Core para crear el cliente HTTP y realizar operaciones `GET`, `POST`, `PUT` y `DELETE`. Este código se agrega a los archivos de código subyacente *.cshtml.cs*. El código para representar los datos en los archivos *.cshtml* está completo.

## Objetivos

Después de completar este ejercicio, podrás hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementar operaciones HTTP en Razor Pages de ASP.NET Core

## Requisitos previos

Para completar el ejercicio, se necesita que las herramientas siguientes estén instaladas en el equipo:

* [Visual Studio Code](https://code.visualstudio.com)
* [El SDK de .NET 7.0 más reciente](https://dotnet.microsoft.com/download/dotnet/7.0)
* [La extensión C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code

**Tiempo estimado para finalizar este ejercicio**: 30 minutos

## Escenario del ejercicio

Este ejercicio tiene dos componentes:

* Una aplicación que envía solicitudes HTTP a una API. La aplicación se ejecuta en `http://localhost:5010`
* Una API que responde a solicitudes HTTP. La API se ejecuta en `http://localhost:5050`

![Decorativas](media/02-architecture.png)


## Descargar el código

En esta sección, descargarás el código para la aplicación web Fruit y Fruit API. También puedes ejecutar Fruit API Iocalmente para que esté disponible para la aplicación web.

### Tarea 1: Descarga y ejecución del código de API

1. Haz clic con el botón derecho en el siguiente vínculo y selecciona la opción **Guardar vínculo como**. 

    * [Código de proyecto FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Inicia el **Explorador de archivos** y ve a la ubicación donde se guardó el archivo.

1. Descomprime el archivo en su propia carpeta.

1. Abre **Terminal Windows** o un **símbolo del sistema** y ve a la ubicación en la que extrajiste el código de la API.

1. En el panel **Terminal Windows**, ejecuta el comando `dotnet` siguiente:

    ```
    dotnet run
    ```

1. Abajo verás un ejemplo de la salida que se genera. Observa la línea `Now listening on: http://localhost:5050` de la salida. Identifica el host y el puerto de la API.

    ```
    info: Microsoft.EntityFrameworkCore.Update[30100]
          Saved 3 entities to in-memory store.
    info: Microsoft.Hosting.Lifetime[14]
          Now listening on: http://localhost:5050
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: 
          <project location>
    ```

>**Nota:** Deja que Fruit API se ejecute durante el resto del ejercicio. 

### Tarea 2: Descarga y apertura del proyecto de aplicación web

1. Haz clic con el botón derecho en el siguiente vínculo y selecciona la opción **Guardar vínculo como**. 

    * [Código subyacente del proyecto de la aplicación web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-codebehind.zip)

1. Inicia el **Explorador de archivos** y ve a la ubicación donde se guardó el archivo.

1. Descomprime el archivo en su propia carpeta.

1. Inicia Visual Studio Code, selecciona **Archivo** y, luego, **Abrir carpeta...** en la barra de menús.

1. Ve a la ubicación en la que descomprimiste los archivos del proyecto y selecciona la carpeta *FruitWebApp-codebehind*.

1. La estructura del proyecto en el panel **Explorer** debe ser similar a la captura de pantalla siguiente. Si el panel **Explorer** no está visible, selecciona **Vista** y, luego, **Explorer** en la barra de menús.

    ![Captura de pantalla que muestra la estructura del proyecto de la aplicación web Fruit.](media/02-web-app-cb-struture.png)

>**Nota:** Dedica tiempo a revisar el código de cada uno de los archivos que se están editando en este ejercicio. Tiene numerosos comentarios y puede ayudarte a comprender la base del código.

## Implementación de código para el cliente HTTP y operación `GET`

La aplicación web Fruit muestra los datos de ejemplo de API en la página principal. Debes agregar código para implementar tanto el cliente HTTP como la operación `GET`, de modo que la aplicación web tenga datos para mostrarlos en la página principal al compilar y ejecutar por primera vez.

### Tarea 1: Implementación del cliente HTTP

1. Selecciona el archivo *Program.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `// Begin HTTP client code` y `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/fruitlist/");
    });
    ```

1. Guarde los cambios en *Program.cs*.

### Tarea 2: Implementación de la operación GET

1. Selecciona el archivo *Index.cshtml.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `// Begin GET operation code` y `// End GET operation code`.

    ```csharp
    // OnGet() is async since HTTP requests should be performed async
      public async Task OnGet()
      {
          // Create the HTTP client using the FruitAPI named factory
          var httpClient = _httpClientFactory.CreateClient("FruitAPI");

          // Perform the GET request and store the response. The empty parameter
          // in GetAsync doesn't modify the base address set in the client factory 
          using HttpResponseMessage response = await httpClient.GetAsync("");

          // If the request is successful deserialize the results into the data model
          if (response.IsSuccessStatusCode)
          {
              using var contentStream = await response.Content.ReadAsStreamAsync();
              FruitModels = await JsonSerializer.DeserializeAsync<IEnumerable<FruitModel>>(contentStream);
          }
      }
    ```

1. Guarda los cambios en *Index.cshtml.cs*.

1. Revisa el código del archivo *Index.cshtml.cs*. Ten en cuenta dónde se agrega `IHttpClientFactory` a la página con inserción de dependencias. Observa también que el modelo de datos está enlazado a la página mediante el atributo `[BindProperty]`.

### Tarea 3: Ejecución de la aplicación web

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web y se muestran los datos de ejemplo de la API, como en la captura de pantalla siguiente.

    ![Captura de pantalla de la aplicación web que muestra los datos de ejemplo.](media/02-web-app-get-sample-data.png)

    >**Nota:** Más adelante, en el ejercicio, agregarás código para habilitar las funciones de agregar, editar y eliminar de la aplicación web. 

    >**Nota:** Puedes omitir de forma segura la pregunta siguiente si aparece al ejecutar la aplicación.

    ![Captura de pantalla de la pregunta para instalar un certificado autofirmado.](media/install-cert.png)

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para las operaciones POST, PUT y DELETE

En esta sección, agregarás código al proyecto para habilitar las funciones **Agregar a la lista**, **Editar** y **Eliminar** en la aplicación web. 

### Tarea 1: Implementación de la operación POST

1. Selecciona el archivo *Add.cshtml.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `// Begin POST operation code` y `// End POST operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Serialize the information to be added to the database
        var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
            Encoding.UTF8,
            "application/json");
    
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Execute the POST request and store the response. The parameters in PostAsync 
        // direct the POST to use the base address and passes the serialized data to the API
        using HttpResponseMessage response = await httpClient.PostAsync("", jsonContent);
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was added successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    }
    ```

1. Guarda los cambios en *Add.cshtml.cs* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Selecciona el botón **Agregar a la lista** y rellena el formulario generado. Después selecciona el botón **Crear**.

1. Comprueba que la adición aparece en la parte inferior de la lista. El mensaje que aparece cerca de la parte superior de la página notifica si hubo incidencias o no.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

### Tarea 1: Implementación de la operación PUT

1. Selecciona el archivo *Edit.cshtml.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `// Begin PUT operation code` y `// End PUT operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
        {
            // Serialize the information to be edited in the database
            var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
                Encoding.UTF8,
                "application/json");
    
            // Create the HTTP client using the FruitAPI named factory
            var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
            // Execute the PUT request and store the response. The parameters in PutAsync 
            // appends the item Id to the base address and passes the serialized data to the API
            using HttpResponseMessage response = await httpClient.PutAsync(FruitModels.id.ToString(), jsonContent);
    
            // Return to the home (Index) page and add a temporary success/failure 
            // message to the page.
            if (response.IsSuccessStatusCode)
            {
                TempData["success"] = "Data was edited successfully.";
                return RedirectToPage("Index");
            }
            else
            {
                TempData["failure"] = "Operation was not successful";
                return RedirectToPage("Index");
            }
    
        }
    ```

1. Guarda los cambios en *Edit.cshtml.cs* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Elige un elemento de la lista para editarlo y selecciona el botón **Editar**. 
1. Edita el **Nombre de la fruta** y el campo **¿Está disponible?** y, luego, selecciona **Actualizar**.

1. Comprueba que los cambios aparezcan en la lista. El mensaje que aparece cerca de la parte superior de la página notifica si hubo incidencias o no.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

### Tarea 1: Implementación de la operación DELETE

1. Selecciona el archivo *Delete.cshtml.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `// Begin DELETE operation code` y `// End DELETE operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Appends the data Id for deletion to the base address and performs the operation
        using HttpResponseMessage response = await httpClient.DeleteAsync(FruitModels.id.ToString());
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was deleted successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    
    }
    ```

1. Guarda los cambios en *Delete.cshtml.cs* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Elige un elemento de la lista para eliminarlo y selecciona el botón **Eliminar**. 

1. Edita el **Nombre de la fruta** y el campo **¿Está disponible?** y, luego, selecciona **Actualizar**.

1. Comprueba que el elemento ya no aparezca en la lista. El mensaje que aparece cerca de la parte superior de la página notifica si hubo incidencias o no.

Cuando tengas todo listo para finalizar el ejercicio:

* Cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**. 

* Para detener Fruit API, pulsa **Ctrl+C** en el terminal en el que se ejecuta.

## Revisar

En este ejercicio aprenderás a hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementación de operaciones HTTP en el código subyacente de Razor Pages de ASP.NET Core
