
# De este comando que he introducido:
```az servicebus queue create --resource-group $resourceGroup  --namespace-name $namespaceName  --name myqueue```
# Que variables he creado y para que sirve el comando

El comando crea **una cola de Azure Service Bus** dentro de un **namespace** y un **grupo de recursos**.  
Las variables que has usado y su función se entienden por cómo Azure CLI interpreta estos parámetros.

---

### 🎯 Resumen rápido
Has creado **dos variables**: **`$resourceGroup`** y **`$namespaceName`**.  
El comando **crea una cola llamada `myqueue`** dentro del namespace indicado.

---

### 🧩 Variables que has creado
Cada parámetro del comando usa una variable que tú definiste antes en tu terminal:

- **Variable resourceGroup** — Contiene el **nombre del grupo de recursos** de Azure donde está el namespace.  
  Un *resource group* es un contenedor lógico donde se agrupan recursos de Azure.

- **Variable namespaceName** — Contiene el **nombre del namespace de Service Bus**.  
  Un *namespace* es el contenedor donde viven colas, topics y suscripciones de Service Bus.

Estas variables no las crea el comando; **tú las definiste previamente** con algo como:

```bash
resourceGroup="MiGrupo"
namespaceName="MiNamespace"
```

---

### 🛠️ ¿Qué hace el comando?
El comando:

```bash
az servicebus queue create \
  --resource-group $resourceGroup \
  --namespace-name $namespaceName \
  --name myqueue
```

hace lo siguiente:

- **Crea una cola de Service Bus** llamada **`myqueue`**  
- La crea **dentro del namespace** cuyo nombre está en `$namespaceName`  
- Ese namespace debe existir dentro del **grupo de recursos** `$resourceGroup`  
- La operación es **idempotente**: si la cola ya existe, no la duplica (según la documentación oficial de Azure)   [Microsoft Learn](https://learn.microsoft.com/es-es/cli/azure/servicebus/queue?view=azure-cli-latest)

---

### 📦 Visualización conceptual

<img width="1535" height="1083" alt="image" src="https://github.com/user-attachments/assets/b0ed819c-79ba-49c7-aa57-fd9570f5a0ec" />
<img width="1377" height="847" alt="image" src="https://github.com/user-attachments/assets/18f44864-9742-46f0-bf5b-13ece9e051f3" />
<img width="824" height="406" alt="image" src="https://github.com/user-attachments/assets/1b81ed5a-93f4-4ccc-80e3-688aae1bfbce" />
<img width="585" height="471" alt="image" src="https://github.com/user-attachments/assets/06bf0ec9-8d48-4e51-ac7e-a2dabfdac699" />

---

### 📘 ¿Para qué sirve una cola de Service Bus?
Una cola permite comunicación **asíncrona** entre aplicaciones:  
un productor envía mensajes y un consumidor los procesa después, siguiendo FIFO (primero en entrar, primero en salir).   [Microsoft Learn](https://learn.microsoft.com/es-es/azure/service-bus-messaging/service-bus-quickstart-cli)

---

Si quieres, puedo explicarte **cómo enviar y recibir mensajes**, o **cómo crear topics y suscripciones**.


