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

# ¿Quieres que revisemos juntos los errores que te aparecían antes de corregirlo?
Puedo explicarte cada uno para que entiendas qué fallaba exactamente.
