---
lab:
  title: 'Ejercicio: Implementación de operaciones HTTP en Razor Pages de ASP.NET Core'
  module: 'Module: Implement HTTP operations in ASP.NET Core Razor Pages'
---

En este ejercicio, aprenderá a agregar el código a una aplicación Razor Pages de ASP.NET Core para crear el cliente HTTP y realizar operaciones `GET`, `POST`, `PUT`, y `DELETE`. Este código se agrega a los archivos de código subyacente *.cshtml.cs*. El código para representar los datos en los archivos *.cshtml* está completo.

## Objetivos

Después de completar este ejercicio, podrá hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementar operaciones HTTP en Razor Pages de ASP.NET Core

## Requisitos previos

Para completar el ejercicio, necesita que las herramientas siguientes estén instaladas en el sistema:

* [Visual Studio Code.](https://code.visualstudio.com)
* [El SDK de .NET 7.0 más reciente.](https://dotnet.microsoft.com/download/dotnet/7.0)
* La [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.

**Tiempo estimado para finalizar este ejercicio**: 30 minutos

## Escenario del ejercicio

Este ejercicio tiene dos componentes:

* Una aplicación que envía solicitudes HTTP a una API. La aplicación se ejecuta en `http://localhost:5010`
* Una API que responde a solicitudes HTTP. La API se ejecuta en `http://localhost:5050`

![Decorativas](media/02-architecture.png)


## Descargar el código

En esta sección descargará el código de la aplicación web Fruit y la Fruit API. También puede ejecutar la Fruit API localmente para que esté disponible para la aplicación web.

### Tarea 1: descargar y ejecutar el código de API

1. Haga clic con el botón derecho en el siguiente vínculo y seleccione la opción **Guardar vínculo como**. 

    * [Código de proyecto de FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Inicie **Explorador de archivos** y vaya a la ubicación donde se guardó el archivo.

1. Descomprima el archivo en su propia carpeta.

1. Abra **Terminal Windows** o un **símbolo del sistema** y vaya a la ubicación en la que extrajo el código de la API.

1. En el panel **Terminal Windows**, ejecute el siguiente comando `dotnet`:

    ```
    dotnet run
    ```

1. El siguiente es un ejemplo de la salida generada. Anote la  línea `Now listening on: http://localhost:5050` de la salida. Identifica el host y el puerto de la API.

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

>**Nota:** Deje que Fruit API se ejecute en el resto del ejercicio. 

### Tarea 2: descargar y abrir el proyecto de aplicación web

1. Haga clic con el botón derecho en el siguiente vínculo y seleccione la opción **Guardar vínculo como**. 

    * [Código de proyecto subyacente de la aplicación web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-codebehind.zip)

1. Inicie **Explorador de archivos** y vaya a la ubicación donde se guardó el archivo.

1. Descomprima el archivo en su propia carpeta.

1. Inicie Visual Studio Code y seleccione **Archivo** y elija **Abrir carpeta...** en la barra de menús.

1. Vaya a la ubicación donde descomprimió los archivos del proyecto y seleccione la carpeta *FruitWebApp-codebehind*.

1. La estructura del proyecto en el panel **Explorador** debe ser similar a la captura de pantalla siguiente. Si el panel **Explorador** no está visible, seleccione **Ver** y, a continuación, **Explorador** en la barra de menús.

    ![Captura de pantalla que muestra la estructura del proyecto de aplicación web Fruit.](media/02-web-app-cb-struture.png)

>**Nota:** Dedique tiempo a revisar el código de cada uno de los archivos que se están editando en este ejercicio. El código está muy comentado y puede ayudarle a comprender la base de código.

## Implementación de código para el cliente HTTP y la operación `GET`

La aplicación web Fruit muestra los datos de ejemplo de API en la página principal. Debe agregar código para implementar el cliente HTTP y la operación `GET`, por lo que la aplicación web tendrá datos para mostrarlos en la página principal al compilarla y ejecutarla por primera vez.

### Tarea 1: implementar el cliente HTTP

1. Seleccione el archivo *Program.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin HTTP client code` y `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/fruitlist/");
    });
    ```

1. Guarde los cambios en *Program.cs*.

### Tarea 2: implementar la operación `GET`

1. Seleccione el archivo *Index.cshtml.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin GET operation code` y `// End GET operation code`.

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

1. Guarde los cambios en *Index.cshtml.cs*.

1. Revise el código del archivo *Index.cshtml.cs*. Tenga en cuenta dónde se agrega `IHttpClientFactory` a la página con inserción de dependencias. Tenga en cuenta también que el modelo de datos está enlazado a la página mediante el atributo `[BindProperty]`.

### Tarea 3: ejecutar la aplicación web

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución y mostrar los datos de muestra de la API, como se muestra en la captura de pantalla siguiente.

    ![Captura de pantalla de la aplicación web que muestra los datos de ejemplo.](media/02-web-app-get-sample-data.png)

    >**Nota:** Más adelante en el ejercicio se agrega código para habilitar la funcionalidad agregar, editar y eliminar de la aplicación web. 

    >**Nota:** Puede omitir de forma segura el mensaje siguiente si aparece al ejecutar la aplicación.

    ![Captura de pantalla del símbolo de sistema para instalar un certificado autofirmado.](media/install-cert.png)

1. Para continuar con el ejercicio, cierre el explorador o la pestaña del explorador y, en Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o **Mayús + F5**.

## Implemente el código de las operaciones `POST`, `PUT` y `DELETE`

En esta sección, agregará código al proyecto para habilitar la funcionalidad **Agregar a la lista**, **Editar** y **Eliminar** en la aplicación web. 

### Tarea 1: implementar la operación `POST`

1. Seleccione el archivo *Add.cshtml.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin POST operation code` y `// End POST operation code`.

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

1. Guarde los cambios en *Add.cshtml.cs* y revise los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Seleccione el botón **Agregar a la lista** y rellene el formulario generado. Después, seleccione el botón **Crear**.

1. Compruebe que la adición aparece en la parte inferior de la lista. El mensaje de éxito o error cerca de la parte superior de la página le notificará si se produjo un problema.

1. Para continuar con el ejercicio, cierre el explorador o la pestaña del explorador y, en Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o **Mayús + F5**.

### Tarea 1: implementar la operación `PUT`

1. Seleccione el archivo *Edit.cshtml.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin PUT operation code` y `// End PUT operation code`.

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

1. Guarde los cambios en *Edit.cshtml.cs* y revise los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Elija un elemento de la lista para editar y seleccione el botón **Editar**. 
1. Edite el **nombre de la fruta** y el campo **¿Está disponible?** y, a continuación, seleccione **Actualizar**.

1. Compruebe que los cambios aparecen en la lista. El mensaje de éxito o error cerca de la parte superior de la página le notificará si se produjo un problema.

1. Para continuar con el ejercicio, cierre el explorador o la pestaña del explorador y, en Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o **Mayús + F5**.

### Tarea 1: implementar la operación `DELETE`

1. Seleccione el archivo *Delete.cshtml.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin DELETE operation code` y `// End DELETE operation code`.

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

1. Guarde los cambios en *Delete.cshtml.cs* y revise los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Elija un elemento de la lista que quiera eliminar y seleccione el botón **Eliminar**. 

1. Edite el **nombre de la fruta** y el campo **¿Está disponible?** y, a continuación, seleccione **Actualizar**.

1. Compruebe que el elemento no aparece en la lista. El mensaje de éxito o error cerca de la parte superior de la página le notificará si se produjo un problema.

Cuando tenga todo listo para finalizar el ejercicio:

* Cierre el explorador o la pestaña del explorador y, en Visual Studio Code, seleccione **Ejecutar depuración de finalización de \|** o **Mayús + F5**. 

* Detenga Fruit API escribiendo `Ctrl + C` en el terminal en el que se ejecuta.

## Revisar

En este ejercicio ha aprendido a hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementar operaciones HTTP en archivos de código subyacente de Razor Pages de ASP.NET Core
