---
lab:
  title: 'Ejercicio: Implementación de operaciones HTTP en aplicaciones web Blazor de ASP.NET Core'
  module: 'Module: Implement HTTP operations in ASP.NET Core Blazor Web apps'
---

En este ejercicio, aprenderás a agregar código a una aplicación web Blazor de ASP.NET Core para crear el cliente HTTP y realizar las operaciones `GET`, `POST`, `PUT`, y `DELETE`. Este código se agrega a los archivos de código subyacente *.razor.cs*. El código para representar los datos en los archivos *.razor* está completo.

## Objetivos

Después de completar este ejercicio, podrá hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementar operaciones HTTP en aplicaciones web Blazor de ASP.NET Core

## Requisitos previos

Para completar el ejercicio, necesita que las herramientas siguientes estén instaladas en el sistema:

* [Visual Studio Code](https://code.visualstudio.com)
* [El SDK de .NET 8.0 más reciente](https://dotnet.microsoft.com/download/dotnet/8.0)
* La [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.

**Tiempo estimado para finalizar este ejercicio**: 30 minutos

## Escenario del ejercicio

Este ejercicio tiene dos componentes:

* Una aplicación que envía solicitudes HTTP a una API. La aplicación web se ejecuta en `http://localhost:5010`.
* Una API que responde a solicitudes HTTP. La API se ejecuta en `http://localhost:5050`.

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

## Implementar código para el cliente HTTP y operaciones de HTTP 

La aplicación web Fruit muestra los datos de ejemplo de la API en la página principal y tiene funcionalidades de adición, edición y eliminación. Debes agregar código para implementar las operaciones del cliente HTTP. 

### Tarea 1: implementar el cliente HTTP

1. Seleccione el archivo *Program.cs* en el panel **Explorador** para abrir el archivo en el editor.

1. Agregue el código siguiente entre los comentarios `// Begin HTTP client code` y `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/");
    });
    ```

1. Guarde los cambios en *Program.cs*.

### Tarea 2: Implementación de la operación GET

1. Selecciona el archivo *home.razor.cs* en el panel **Explorador** para abrirlo y editarlo. Se encuentra en la carpeta `Components/Pages`.

1. Agregue el código siguiente entre los comentarios `// Begin GET operation code` y `// End GET operation code`.

    ```csharp
    protected override async Task OnInitializedAsync()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Perform the GET request and store the response. The parameter
        // in GetAsync specifies the endpoint in the API 
        using HttpResponseMessage response = await httpClient.GetAsync("/fruits");

        // If the request is successful deserialize the results into the data model
        if (response.IsSuccessStatusCode)
        {
            using var contentStream = await response.Content.ReadAsStreamAsync();
            _fruitList = await JsonSerializer.DeserializeAsync<IEnumerable<FruitModel>>(contentStream);
        }
        else
        {
            // If the request is unsuccessful, log the error message
            Console.WriteLine($"Failed to load fruit list. Status code: {response.StatusCode}");
        }
    }
    ```

1. Guarda los cambios en *Home.razor.cs*.

1. Revisa el código en el archivo *Home.razor.cs*. Tenga en cuenta dónde se agrega `IHttpClientFactory` a la página con inserción de dependencias.

### Tarea 3: Implementación de la operación POST

1. Selecciona el archivo *Add.razor.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `// Begin POST operation code` y `// End POST operation code`.

    ```csharp
    private async Task Submit()
    {
        // Serialize the information to be added to the database
        var jsonContent = new StringContent(JsonSerializer.Serialize(_fruitList),
            Encoding.UTF8,
            "application/json");

        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Execute the POST request and store the response. The response will contain the new record's ID
        using HttpResponseMessage response = await httpClient.PostAsync("/fruits", jsonContent);

        // Check if the operation was successful, and navigate to the home page if it was
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to add fruit. Status code: {response.StatusCode}");
        }
    }
    ```

1. Guarda los cambios en *Add.razor.cs* y revisa los comentarios en el código.

### Tarea 4: Implementación de la operación PUT

1. Selecciona el archivo *Edit.razor.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `// Begin PUT operation code` y `// End PUT operation code`.

    ```csharp
    private async Task Submit()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Store the updated data in a JSON object
        var jsonContent = new StringContent(JsonSerializer.Serialize(_fruitList), 
            Encoding.UTF8, "application/json");

        // Execute the PUT request
        using HttpResponseMessage response = await httpClient.PutAsync($"/fruits/{Id}", jsonContent);

        // If the response is successful, navigate back to the home page 
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to update fruit with edits. Status code: {response.StatusCode}");
        }
    }
    ```

1. Guarda los cambios en *Edit.razor.cs* y revisa los comentarios en el código.

### Tarea 5: Implementación de la operación DELETE

1. Selecciona el archivo *Delete.razor.cs* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `// Begin DELETE operation code` y `// End DELETE operation code`.

    ```csharp
    private async Task Submit()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Execute the DELETE request and store the response
        using HttpResponseMessage response = await httpClient.DeleteAsync("/fruits/" + Id.ToString());

        // Return to the home page 
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to delete fruit. Status code: {response.StatusCode}");
        }
    }
    ```

1. Guarda los cambios en *Delete.razor.cs* y revisa los comentarios en el código.

## Ejecute y pruebe la aplicación web

### Tarea 1: Ejecución de la aplicación web

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución y mostrar los datos de muestra de la API, como se muestra en la captura de pantalla siguiente.

    ![Captura de pantalla de la aplicación web que muestra los datos de ejemplo.](media/02-web-app-get-sample-data.png)

    >**Nota:** Puede omitir de forma segura el mensaje siguiente si aparece al ejecutar la aplicación.

    ![Captura de pantalla del símbolo de sistema para instalar un certificado autofirmado.](media/install-cert.png)

### Tarea 1: Prueba de la aplicación web

1. Seleccione el botón **Agregar a la lista** y rellene el formulario generado. Después, seleccione el botón **Crear**.

1. Compruebe que la adición aparece en la parte inferior de la lista.

1. Elija un elemento de la lista para editar y seleccione el botón **Editar**. 
1. Edite el **nombre de la fruta** y el campo **¿Está disponible?** y, a continuación, seleccione **Actualizar**.

1. Compruebe que los cambios aparecen en la lista. 

1. Elija un elemento de la lista que quiera eliminar y seleccione el botón **Eliminar**.

1. En la página Eliminar, compruebe que se muestra el elemento seleccionado y haga clic en el botón **Eliminar**.

1. Compruebe que el elemento no aparece en la lista.

Cuando tenga todo listo para finalizar el ejercicio:

* Cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**. 

* Para detener Fruit API, pulsa **Ctrl+C** en el terminal en el que se ejecuta.

## Revisar

En este ejercicio ha aprendido a hacer lo siguiente:

* Implementar `IHttpClientFactory` como cliente HTTP
* Implementar operaciones HTTP en archivos de código subyacente de Blazor de ASP.NET Core
