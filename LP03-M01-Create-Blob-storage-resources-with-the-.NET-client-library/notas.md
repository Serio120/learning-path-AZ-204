# En Español

Perfecto, ya puedo ver tu `Program.cs` y **sí, tiene varios errores graves de sintaxis y de estructura**. No te preocupes, te explico exactamente qué está mal y te dejo una versión **corregida y funcional**.

---

# 🚨 **Problemas principales en tu código**

### ❌ 1. Error tipográfico al inicio  
Tienes:

```csharp
sing Azure.Storage.Blobs;
```

Debe ser:

```csharp
using Azure.Storage.Blobs;
```

---

### ❌ 2. Llaves `{}` completamente desordenadas  
Tu método `ProcessAsync()` se cierra antes de tiempo, y luego el resto del código queda **fuera del método**, lo cual es inválido.

---

### ❌ 3. Aparecen bloques como `examples{ ... }`  
Eso **no existe en C#**. Probablemente querías escribir:

```csharp
else
{
}
```
---

### ❌ 4. Código duplicado y fuera de contexto  
Varias secciones están fuera del método asíncrono, lo que rompe la compilación.

---

# ✅ **Aquí tienes una versión corregida y funcional de tu Program.cs**

> ✔ Mantiene tu lógica  
> ✔ Usa DefaultAzureCredential  
> ✔ Crea contenedor, sube archivo, lista blobs y descarga  
> ✔ Sin errores de sintaxis  

CÓPIALO COMPLETO:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Identity;

Console.WriteLine("Azure Blob Storage exercise\n");

// Configurar credenciales
DefaultAzureCredentialOptions options = new()
{
    ExcludeEnvironmentCredential = true,
    ExcludeManagedIdentityCredential = true
};

// Ejecutar proceso principal
await ProcessAsync();

Console.WriteLine("\nPress enter to exit the sample application.");
Console.ReadLine();

async Task ProcessAsync()
{
    // Crear cliente de Blob Storage
    string accountName = "storageacct18807"; // Cambia por tu cuenta
    DefaultAzureCredential credential = new DefaultAzureCredential(options);

    string blobServiceEndpoint = $"https://{accountName}.blob.core.windows.net";
    BlobServiceClient blobServiceClient = new BlobServiceClient(new Uri(blobServiceEndpoint), credential);

    // Crear contenedor
    string containerName = "wtblob" + Guid.NewGuid().ToString();
    Console.WriteLine("Creating container: " + containerName);

    BlobContainerClient containerClient =
        await blobServiceClient.CreateBlobContainerAsync(containerName);

    if (containerClient == null)
    {
        Console.WriteLine("Failed to create the container, exiting program.");
        return;
    }

    Console.WriteLine("Container created successfully, press 'Enter' to continue.");
    Console.ReadLine();

    // Crear archivo local
    Console.WriteLine("Creating a local file for upload to Blob storage...");
    string localPath = "./data/";
    Directory.CreateDirectory(localPath);

    string fileName = "wtfile" + Guid.NewGuid().ToString() + ".txt";
    string localFilePath = Path.Combine(localPath, fileName);

    await File.WriteAllTextAsync(localFilePath, "Hello, World!");
    Console.WriteLine("Local file created, press 'Enter' to continue.");
    Console.ReadLine();

    // Subir archivo
    BlobClient blobClient = containerClient.GetBlobClient(fileName);

    Console.WriteLine($"Uploading to Blob storage as blob:\n\t {blobClient.Uri}");

    using (FileStream uploadFileStream = File.OpenRead(localFilePath))
    {
        await blobClient.UploadAsync(uploadFileStream);
    }

    bool blobExists = await blobClient.ExistsAsync();
    if (!blobExists)
    {
        Console.WriteLine("File upload failed, exiting program.");
        return;
    }

    Console.WriteLine("File uploaded successfully, press 'Enter' to continue.");
    Console.ReadLine();

    // Listar blobs
    Console.WriteLine("Listing blobs in container...");
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        Console.WriteLine("\t" + blobItem.Name);
    }

    Console.WriteLine("Press 'Enter' to continue.");
    Console.ReadLine();

    // Descargar blob
    string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");
    Console.WriteLine($"Downloading blob to: {downloadFilePath}");

    BlobDownloadInfo download = await blobClient.DownloadAsync();

    using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
    {
        await download.Content.CopyToAsync(downloadFileStream);
    }

    Console.WriteLine($"Blob downloaded successfully to: {downloadFilePath}");
}
```

---

# 🎉 **Ahora sí podrás compilar sin errores**

En Cloud Shell o en tu VM:

```bash
dotnet build
dotnet run
```
---
---

Para renombrar un archivo en Azure Cloud Shell, el comando exacto depende del entorno que tengas seleccionado en la barra de herramientas de tu consola: Bash (Linux) o PowerShell (Windows). [1] 
## Si usas el entorno Bash (Linux)
En Bash debes emplear el comando mv (move), especificando primero el nombre actual del archivo y después el nuevo nombre. [2, 3, 4] 

mv nombre_actual.txt nuevo_nombre.txt

## Si usas el entorno PowerShell
En PowerShell debes utilizar el cmdlet Rename-Item, o en su defecto el alias estándar ren o move. [5, 6] 

Rename-Item -Path "nombre_actual.txt" -NewName "nuevo_nombre.txt"

(O de forma abreviada: ren nombre_actual.txt nuevo_nombre.txt)
------------------------------
## Cómo renombrar desde el editor gráfico de Cloud Shell
Si prefieres no utilizar comandos de consola, Azure Cloud Shell incluye un editor gráfico integrado: [7, 8] 

   1. Escribe el comando code . en la terminal para desplegar el editor en pantalla.
   2. En la barra lateral izquierda aparecerá el explorador de archivos.
   3. Haz clic derecho sobre el archivo que quieres modificar.
   4. Selecciona la opción Rename (Renombrar), escribe el nuevo nombre y presiona Enter. [7, 9, 10, 11, 12] 

Si necesitas algo más específico, indícame:

* ¿Estás intentando renombrar un archivo local del directorio personal u un objeto dentro de un Azure Storage Account?
* ¿Te ha aparecido algún mensaje de error al intentar cambiarlo?


[1] [https://learn.microsoft.com](https://learn.microsoft.com/es-es/azure/cloud-shell/use-the-shell-window-new)
[2] [https://www.manageengine.com](https://www.manageengine.com/products/eventlog/kb/linux/how-to-rename-files-in-linux.html)
[3] [https://www.hostinger.com](https://www.hostinger.com/ar/tutoriales/renombrar-archivos-linux)
[4] [https://computernewage.com](https://computernewage.com/2023/03/31/gnu-linux-scripting-bash-ejemplo-6/)
[5] [https://netwrix.com](https://netwrix.com/es/resources/blog/powershell-rename-file/)
[6] [https://netwrix.com](https://netwrix.com/es/resources/blog/powershell-rename-file/)
[7] [https://learn.microsoft.com](https://learn.microsoft.com/es-es/azure/cloud-shell/use-cloud-shell-editor-classic)
[8] [https://learn.microsoft.com](https://learn.microsoft.com/es-es/azure/cloud-shell/use-the-shell-window-new)
[9] [https://azure.microsoft.com](https://azure.microsoft.com/es-es/get-started/azure-portal/cloud-shell)
[10] [https://es.linkedin.com](https://es.linkedin.com/learning/visual-studio-integracion-git-esencial/renombrado-de-archivos-en-la-carpeta-de-un-repositorio-local-a-traves-de-visual-studio)
[11] [https://help.ubuntu.com](https://help.ubuntu.com/stable/ubuntu-help/files-rename.html.es)
[12] [https://docs.github.com](https://docs.github.com/es/enterprise-cloud@latest/repositories/working-with-files/managing-files/renaming-a-file)

---

Para editar código en un archivo .cs (C#) directamente desde Azure Cloud Shell, la herramienta oficial, más rápida y visual es el editor integrado basado en Monaco (el motor de Visual Studio Code). [1] 
Aquí tienes los pasos exactos y las alternativas disponibles:
## Opción 1: El editor integrado (Recomendado)
Es una interfaz gráfica ligera que se abre dentro de la misma ventana del Cloud Shell. [2] 

   1. Abre el archivo ejecutando el comando code seguido del nombre del archivo:
   
   code archivo.cs
   
   2. Se abrirá una ventana de edición sobre la terminal con resaltado de sintaxis para C#.
   3. Modifica tu código directamente.
   4. Para guardar los cambios, presiona Ctrl + S (Windows/Linux) o Cmd + S (macOS).
   5. Para cerrar el editor y volver a la consola, presiona Ctrl + Q o haz clic en los tres puntos ... de la esquina superior derecha y selecciona Close Editor. [3] 

## Opción 2: Editores de terminal clásicos (Solo en entorno Bash)
Si estás utilizando el entorno Bash de Cloud Shell, también tienes a tu disposición los editores de texto tradicionales de la consola de Linux:

* Nano (El más sencillo si no usas el editor gráfico):

nano archivo.cs

Modificas el texto, guardas con Ctrl + O -> Enter, y sales con Ctrl + X.
* Vim (Para usuarios avanzados):

vim archivo.cs


Para ayudarte mejor con tu desarrollo, dime:

* ¿Estás intentando compilar y ejecutar el archivo .cs en Cloud Shell?
* ¿Estás trabajando en un proyecto completo de .NET Core o es un script suelto?


[1] [https://ingenieriadesoftware.es](https://ingenieriadesoftware.es/editor-codigo-online-programa-ide-navegador/)
[2] [https://azure.microsoft.com](https://azure.microsoft.com/es-es/get-started/azure-portal/cloud-shell)
[3] [https://learn.microsoft.com](https://learn.microsoft.com/es-es/azure/cloud-shell/use-cloud-shell-editor-new)

