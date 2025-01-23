---
lab:
  title: "Ejercicio: Representación de respuestas de API en las aplicaciones web Blazor de ASP.NET\_Core"
  module: 'Module: Render API responses in ASP.NET Core Blazor Web apps'
---

En este ejercicio, aprenderás a agregar código a una aplicación web Blazor de ASP.NET Core para representar resultados de operaciones HTTP. Este código se agrega a los archivos *.razor*. El código que realiza las operaciones en el archivo *.razor.cs* está completo.

## Objetivos

Después de completar este ejercicio, podrá hacer lo siguiente:

* Implementar la sintaxis Razor en una aplicación
* Integrar el código C# con la sintaxis Razor

## Requisitos previos

Para completar el ejercicio, necesita que las herramientas siguientes estén instaladas en el sistema:

* [Visual Studio Code](https://code.visualstudio.com)
* [El SDK de .NET 8.0 más reciente](https://dotnet.microsoft.com/download/dotnet/8.0)
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

    * [Código del proyecto de representación de la aplicación web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-render.zip)

1. Inicie **Explorador de archivos** y vaya a la ubicación donde se guardó el archivo.

1. Descomprima el archivo en su propia carpeta.

1. Inicie Visual Studio Code y seleccione **Archivo** y elija **Abrir carpeta...** en la barra de menús.

1. Vaya a la ubicación donde descomprimió los archivos del proyecto y seleccione la carpeta *FruitWebApp-render*.

1. La estructura del proyecto en el panel **Explorador** debe ser similar a la captura de pantalla siguiente. Si el panel **Explorador** no está visible, seleccione **Ver** y, a continuación, **Explorador** en la barra de menús.

    ![Captura de pantalla que muestra la estructura del proyecto de aplicación web Fruit.](media/03-web-app-render-structure.png)

>**Nota:** Dedique tiempo a revisar el código de cada uno de los archivos que se están editando en este ejercicio. El código está muy comentado y puede ayudarle a comprender la base de código.

## Implementar código para representar datos en la página principal

La aplicación web Fruit muestra los datos de ejemplo de API en la página principal. Debe agregar código para recorrer en iteración los datos de muestra devueltos por la operación HTTP `GET` realizada en el archivo de código subyacente.

### Tarea 1: agregar código para representar datos en una tabla

1. Selecciona el archivo *Home.razor* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `@* Begin render API data code block *@` y `@* End render API data code block *@`.

    ```razor
    <tbody>
        @*  The Razor explicit expression @foreach is used to iterate through the
            data returned to the data model from the HTTP operations. *@
        @foreach (var obj in _fruitList ?? [])
        {
            <tr>
                @* Display the name of the fruit. *@
                <td width="50%">@obj.name</td>
                @*  The following if statment changes the true/false of instock to Yes/No. *@
                @{
                    if (@obj.instock)
                    {
                        <td width="20%" class="text-md-center">
                            Yes
                        </td>
                    }
                    else
                    {
                        <td width="20%" class="text-md-center">
                            No
                        </td>
                    }
                }
                <td width="30%" class="text-center">
                    @* The following div renders the Edit and Delete buttons that pass the Id 
                        to a function that handles the navigation and passes the Id to the page. *@
                    <div class="w-75 btn-group btn-group-sm" role="group" style="text-align:center">
                        <button class="btn btn-primary  mx-2" @onclick="() => EditButton(obj.id)">
                            Edit
                        </button>
                        <button class="btn btn-danger mx-2" @onclick="() => DeleteButton(obj.id)">
                            Delete
                        </button>
                    </div>
                </td>
            </tr>
        }
    </tbody>
    ```

1. Guarda los cambios en *Home.razor* y revisa los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Compruebe que la página Índice muestra los datos de muestra de la API.

    >**Nota:** Las funciones **Agregar a lista**, **Editar** y **Eliminar** no funcionarán hasta que agregue código para ellas más adelante en este ejercicio.

    >**Nota:** Puede omitir de forma segura el mensaje siguiente si aparece al ejecutar la aplicación.

    ![Captura de pantalla del símbolo de sistema para instalar un certificado autofirmado.](media/install-cert.png)

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Agregar a la lista**

Las operaciones de adición, edición y eliminación se controlan en una página *.razor* independiente del proyecto. En esta sección, agregarás código para crear un formulario en el archivo *Add.razor* para habilitar la adición de datos a la lista.

### Tarea 1: agregar código para crear el formulario de agregación de datos

1. Selecciona el archivo *Add.razor* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `@* Begin render Add code block *@` y `@* End render Add code block *@`.

    ```csharp
    @* Data is added using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="AddFruit" Model="_fruitList">
        @*  The _fruitList.id is here so the full data model is represented on the page.
            The database behind the API will assign the id. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Add Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label class="h5"></label><br />
                @* Empty text box for the name of the fruit to be added. *@
                <InputText @bind-Value="_fruitList!.name" />
            </div>
            <div class="mb-3">
                <label class="h5"></label><br />
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" />
                <label class="h7">Check the box if it's available.</label>
            </div>
            @* Submit the addition or return to the Index page if the Add is cancelled.*@
            <button @onclick="() => Submit()" class="btn btn-primary" style="width:150px;">Create</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```
    
1. Guarda los cambios en *Add.razor* y revisa los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Seleccione **Agregar a la lista** en la página.

1. Escriba el nombre de una fruta que desee agregar a la lista y active la casilla para indicar que está disponible.

1. Seleccione **Crear** para agregar la entrada a la lista y se le redirigirá a la página principal. Compruebe que la entrada se agregó a la lista.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Editar**

En esta sección, agregarás código para crear un formulario en el archivo *Edit.cshtml* para habilitar la edición de datos en la lista.

### Tarea 1: agregar código para el formulario de edición

1. Selecciona el archivo *Edit.razor* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `@* Begin render Edit code block *@` y `@* End render Edit code block *@`.

    ```csharp
    @* Data is edited using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="EditFruit" Model="_fruitList">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Edit Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in an editable text box. *@
                <InputText @bind-Value="_fruitList!.name" />
            </div>
            <div class="mb-3">
                <label  class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if available.</label>
            </div>
            @* Submit the changes or return to the Index page if the Edit is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Save</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```

1. Guarda los cambios en *Edit.razor* y revisa los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Elija un elemento de la lista que quiera cambiar y seleccione **Editar** en esa fila.

1. Edite el nombre de la fruta y active la casilla para cambiar su estado de disponibilidad.

1. Seleccione **Actualizar** para guardar los cambios y se le redirigirá a la página principal. Compruebe que el cambio se muestra en la lista.

1. Para continuar con el ejercicio, cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**.

## Implementación de código para controlar la función **Eliminar**

En esta sección, agregarás código para crear un formulario en el archivo *Delete.cshtml* para habilitar la eliminación de datos de la lista.

### Tarea 1: agregar código para el formulario de eliminación

1. Selecciona el archivo *Delete.razor* en el panel **Explorador** para abrirlo y editarlo.

1. Agregue el código siguiente entre los comentarios `@* Begin render Delete code block *@` y `@* End render Delete code block *@`.

    ```csharp
    @* Data is deleted using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="DeleteFruit" Model="_fruitList">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Delete Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in a non-editable text box. *@
                <InputText @bind-Value="_fruitList!.name" Disabled/>
            </div>
            <div class="mb-3">
                <label  class="h5"></label><br/>
                @* Render the true/false instock state from the record in a non-editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" Disabled  />
                <label class="h7">Check the box if available.</label>
            </div>
            @* Submit the changes or return to the Index page if the delete is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Delete</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```

1. Guarda los cambios en *Delete.razor* y revisa los comentarios en el código.

1. En el menú superior de Visual Studio Code, seleccione **Ejecutar depuración de inicio de \|** o seleccione **F5**. Una vez que el proyecto haya terminado de compilar una ventana del explorador, debe iniciarse con la aplicación web en ejecución.

1. Elija un elemento de la lista que quiera eliminar y seleccione **Eliminar** en esa fila.

1. Seleccione **Eliminar** y se le redirigirá de nuevo a la página principal. Compruebe que el elemento que ha eliminado ya no se muestra en la lista.

Cuando tenga todo listo para finalizar el ejercicio:

* Cierra el explorador o la pestaña del explorador y, en Visual Studio Code, selecciona **Ejecutar \| Detener depuración** o pulsa **Mayús+F5**. 

* Para detener Fruit API, pulsa **Ctrl+C** en el terminal en el que se ejecuta.

## Revisar

En este ejercicio ha aprendido a hacer lo siguiente:

* Implementar palabras clave de Razor en una aplicación
* Integrar el código C# con la sintaxis de Razor Pages
