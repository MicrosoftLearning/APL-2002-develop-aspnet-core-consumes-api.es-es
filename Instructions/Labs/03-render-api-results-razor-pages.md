---
lab:
  title: "Ejercicio: Representación de respuestas de API en Razor\_Pages de ASP.NET\_Core"
  module: 'Module: Render API responses in ASP.NET Core Razor Pages'
---

En este ejercicio, aprenderás a agregar código a una aplicación de Razor Pages de ASP.NET Core para representar resultados de operaciones HTTP. Este código se agrega a los archivos *.cshtml*. El código que realiza las operaciones en los archivos *.cshtml.cs* está completo.

## Objetivos

Después de completar este ejercicio, podrás hacer lo siguiente:

* Implementar palabras claves de Razor en una aplicación
* Integrar el código de C# con la sintaxis de Razor Pages

## Requisitos previos

Para completar el ejercicio, se necesita que las herramientas siguientes estén instaladas en el equipo:

* [Visual Studio Code](https://code.visualstudio.com)
* [El SDK de .NET 7.0 más reciente](https://dotnet.microsoft.com/download/dotnet/7.0)
* [La extensión C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code

**Tiempo estimado para finalizar este ejercicio**: 30 minutos

## Escenario del ejercicio

Este ejercicio tiene dos componentes:

* Una aplicación web que envía solicitudes HTTP a una API. La aplicación se ejecuta en `http://localhost:5010`
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

    * [Código del proyecto de representación de la aplicación web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-render.zip)

1. Inicia el **Explorador de archivos** y ve a la ubicación donde se guardó el archivo.

1. Descomprime el archivo en su propia carpeta.

1. Inicia Visual Studio Code, selecciona **Archivo** y, luego, **Abrir carpeta...** en la barra de menús.

1. Ve a la ubicación en la que descomprimiste los archivos del proyecto y selecciona la carpeta *FruitWebApp-render*.

1. La estructura del proyecto en el panel **Explorer** debe ser similar a la captura de pantalla siguiente. Si el panel **Explorer** no está visible, selecciona **Vista** y, luego, **Explorer** en la barra de menús.

    ![Captura de pantalla que muestra la estructura del proyecto de la aplicación web Fruit.](media/03-web-app-render-structure.png)

>**Nota:** Dedica tiempo a revisar el código de cada uno de los archivos que se están editando en este ejercicio. Tiene numerosos comentarios y puede ayudarte a comprender la base del código.

## Implementación de código para representar datos en la página Índice

La aplicación web Fruit muestra los datos de ejemplo de API en la página principal. Debes agregar código para recorrer en iteración los datos de ejemplo devueltos por la operación `GET` de HTTP realizada en el archivo de código subyacente.

### Tarea 1: Adición de código para representar datos en una tabla

1. Selecciona el archivo *Index.cshtml* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `@* Begin render API data code block *@` y `@* End render API data code block *@`.

    ```csharp
    <tbody>
        
        @*  The Razor keyword @foreach is used to iterate through the
            data returned to the data model from the HTTP operations. *@
        @foreach (var obj in Model.FruitModels)
        {
            <tr>
                @* Display the name of the fruit. *@
                <td width="50%">@obj.name</td>
                @*  The following if statment is a Razor code block that changes the color 
                    and icon of the available indicator in the page rendering. *@
                @{
                    if (@obj.instock)
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-check-circle" style="font-size: 1rem; color: green;"></i>&nbsp;Yes
                        </td>
                    }
                    else
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-dash-circle" style="font-size: 1rem; color:red;"></i>&nbsp;No
                        </td>
                    }
                }
                <td width="30%" class="text-center">
                    @*  The following div contains information to handle the edit and delete functions. *@
                    <div class="w-75 btn-group btn-group-sm" role="group" style="text-align:center">
                        @* Routes to the Edit page and passes the id of the record. *@
                        <a asp-page="Edit" asp-route-id="@obj.id" class="btn btn-primary  mx-2">
                            <i class="bi bi-pencil-square"></i> Edit
                        </a>
                        @* Routes to the Delete page and passes the id of the record. *@
                        <a asp-page="Delete" asp-route-id="@obj.id" class="btn btn-danger mx-2">
                            <i class="bi bi-trash"></i> Delete
                        </a>
                    </div>
                </td>
            </tr>
        }
    </tbody>
    ```

1. Guarda los cambios en *Index.cshtml* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Comprueba que la página Índice muestra los datos de ejemplo de la API.

    >**Nota:** Las funciones **Agregar a la lista**, **Editar** y **Eliminar** no funcionarán hasta que agregues código para ellos más adelante en el ejercicio.

    >**Nota:** Puedes omitir de forma segura la pregunta siguiente si aparece al ejecutar la aplicación.

    ![Captura de pantalla de la pregunta para instalar un certificado autofirmado.](media/install-cert.png)

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Agregar a la lista**

Las operaciones de agregar, editar y eliminar se controlan en una página *.cshtml* independiente del proyecto. En esta sección, agregarás código para crear un formulario en el archivo *Add.cshtml* para habilitar la adición de datos a la lista.

### Tarea 1: Adición de código para crear el formulario de agregar datos

1. Selecciona el archivo *Add.cshtml* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `@* Begin render Add code block *@` y `@* End render Add code block *@`.

    ```csharp
    <form method="post">
        @*  The FruitModels.id is here so the full data model is represented on the page.
            The database behind the API will assign the id. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Add Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Empty text box for the name of the fruit to be added. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if it's available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the addition or return to the Index page if the Add is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Create</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Guarda los cambios en *Add.cshtml* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Selecciona **Agregar a la lista** en la página.

1. Escribe el nombre de una fruta que quieras agregar a la lista y marca la casilla para indicar que está disponible.

1. Selecciona **Crear** para agregar la entrada a la lista y se te redirigirá a la página principal. Comprueba si la entrada se agregó a la lista.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Editar**

En esta sección, agregarás código para crear un formulario en el archivo *Edit.cshtml* para habilitar la edición de datos en la lista.

### Tarea 1: Adición de código para el formulario de edición

1. Selecciona el archivo *Edit.cshtml* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `@* Begin render Edit code block *@` y `@* End render Edit code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Edit Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the current name of the fruit in an editable text box. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the edit is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Update</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Guarda los cambios en *Edit.cshtml* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Elige un elemento de la lista para cambiarlo y selecciona **Editar** en esa fila.

1. Edita el nombre de la fruta y marca la casilla para cambiar su estado de disponibilidad.

1. Selecciona **Actualizar** para guardar los cambios y se te redirigirá a la página principal. Comprueba que el cambio se muestra en la lista.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Eliminar**

En esta sección, agregarás código para crear un formulario en el archivo *Delete.cshtml* para habilitar la eliminación de datos de la lista.

### Tarea 1: Adición de código para el formulario de eliminación

1. Selecciona el archivo *Delete.cshtml* en el panel **Explorador** para abrirlo y editarlo.

1. Agrega el siguiente código entre los comentarios `@* Begin render Delete code block *@` y `@* End render Delete code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be avaialable to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Delete Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in a non-editable text box. *@
                <input type="text" asp-for="FruitModels.name" disabled/>
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in a non-editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" disabled  />
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the delete is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Delete</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Guarda los cambios en *Delete.cshtml* y revisa los comentarios del código.

1. En el menú superior de Visual Studio Code, selecciona **Ejecutar \| Iniciar depuración** o pulsa **F5**. Cuando el proyecto haya terminado de compilar, se debe abrir una ventana del explorador en la que se esté ejecutando la aplicación web.

1. Elige un elemento de la lista para eliminarlo y selecciona **Eliminar** en esa fila.

1. Selecciona **Eliminar** y se te redirigirá a la página principal. Comprueba que el elemento que has eliminado ya no se muestra en la lista.

Cuando tengas todo listo para finalizar el ejercicio:

* Cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**. 

* Para detener Fruit API, pulsa **Ctrl+C** en el terminal en el que se ejecuta.

## Revisar

En este ejercicio aprenderás a hacer lo siguiente:

* Implementar palabras claves de Razor en una aplicación
* Integrar el código de C# con la sintaxis de Razor Pages
