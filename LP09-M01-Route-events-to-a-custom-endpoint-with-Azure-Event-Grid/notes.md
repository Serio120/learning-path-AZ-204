Puedes ver todas las variables que has creado en tu *shell* simplemente **imprimiéndolas** después de definirlas. En Bash, las variables existen en el entorno actual mientras dure la sesión, así que puedes consultarlas con `echo` o usando `declare -p`.

---

### ✅ **1. Ver cada variable individualmente**
Después de ejecutar tu comando:

```bash
let rNum=$RANDOM resourceGroup=LP9-HRAUWINO1R location=westus topicName="mytopic-evgtopic-${rNum}" siteName="evgsite-${rNum}" siteURL="https://${siteName}.azurewebsites.net"
```

Puedes verlas así:

```bash
echo $rNum
echo $resourceGroup
echo $location
echo $topicName
echo $siteName
echo $siteURL
```

---

### ✅ **2. Ver todas las variables que empiezan por algo**
Si quieres ver solo las que creaste (por ejemplo, todas las que contienen “site”):

```bash
declare -p | grep site
```

O todas las que contienen “topic”:

```bash
declare -p | grep topic
```

---

### ✅ **3. Ver todas las variables del entorno**
Esto muestra absolutamente todas:

```bash
declare -p
```

O solo las exportadas:

```bash
printenv
```

---

### ⚠️ Nota importante
Tu comando inicial:

```bash
let rNum=$RANDOM resourceGroup=LP9-HRAUWINO1R ...
```

**no exporta** las variables al entorno, solo las define en la sesión actual.  
Si quieres que estén disponibles para procesos hijos, deberías usar:

```bash
export resourceGroup location topicName siteName siteURL
```

---

Si quieres, puedo ayudarte a reescribir tu comando para que sea más claro y fácil de depurar.

---
---
