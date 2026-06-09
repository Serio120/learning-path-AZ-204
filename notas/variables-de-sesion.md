En Azure CLI, las variables de sesión ($variable) se usan para almacenar valores temporalmente en tu shell (Bash, PowerShell o CMD) y reutilizarlos en varios comandos sin tener que escribirlos cada vez.

1️⃣ Variables en Bash (Linux, macOS, WSL)
En Bash, las variables se definen así:
Bash# Definir variable
resourceGroup="miGrupoRecursos"

# Usar variable en un comando Azure CLI
az group create --name $resourceGroup --location eastus


⚠️ En Bash, no uses espacios alrededor del signo =.


2️⃣ Variables en PowerShell (Windows)
En PowerShell, la sintaxis cambia:
Powershell# Definir variable
$resourceGroup = "miGrupoRecursos"

# Usar variable
az group create --name $resourceGroup --location eastus


3️⃣ Variables en CMD (Windows clásico)
En CMD, se usan variables de entorno temporales:
Cmdset resourceGroup=miGrupoRecursos
az group create --name %resourceGroup% --location eastus


4️⃣ Ejemplo práctico con varias variables
Bash# Bash
location="westeurope"
resourceGroup="demoRG"
storageName="almacen$(date +%s)"  # Nombre único con timestamp

az group create --name $resourceGroup --location $location
az storage account create --name $storageName --resource-group $resourceGroup --location $location --sku Standard_LRS


5️⃣ Variables persistentes en Azure CLI
Si quieres que un valor se use por defecto en toda la sesión de Azure CLI:
Bashaz configure --defaults group=$resourceGroup location=$location

Así, no tendrás que pasar --resource-group ni --location en cada comando.

Si quieres, puedo prepararte un script completo que configure variables de sesión y cree recursos en Azure de forma automatizada.
¿Quieres que te lo haga para Bash o PowerShell?
