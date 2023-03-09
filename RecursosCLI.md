# CLI de Azure

## Instalación CLI de Azure

- Linux: **apt-get** en Ubuntu, **yum** en Red Hat y **zypper** en OpenSUSE.

- Mac: **Homebrew**

En un shell de Bash, usará esta sintaxis al establecer las variables:

    variable="value"

    variable=integer

En un entorno de PowerShell para ejecutar scripts de la CLI de Azure, usará esta sintaxis para las variables:

    $variable="value"
    
    $variable=integer

## Ejecutar la CLI de Azure

    az --version

## Administración de Recursos Mediante CLI

    az find blob

    az find "az vm"

    az find "az vm create"

### Creación De Un Recurso de Azure

1. Conexión a la suscripción de Azure.
        
        az login

2. Creación del recurso.

        az group create --name <name> --location <location>

3. Comprobación de la creación.

        az group list
    
        az group list --output table

### Creación de Variables para Almacenar Recursos

    export RESOURCE_GROUP=learn-75e4c72b-5575-465b-8924-65358caabb84
    
    export AZURE_REGION=centralus
    
    export AZURE_APP_PLAN=popupappplan-$RANDOM
    
    export AZURE_WEB_APP=popupwebapp-$RANDOM

Para filtrar las variables según el recurso, se puede usar el siguiente comando:

    az group list --query "[?name == '$RESOURCE_GROUP']"

### Implementar Código Desde Github

    az webapp deployment source config --name $AZURE_WEB_APP --resource-group $RESOURCE_GROUP --repo-url "https://github.com/Azure-Samples/php-docs-hello-world" --branch master --manual-integration
    
## Ejemplos de Implementación de Recursos Con CLI

### Creación MV

    az vm create \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --name my-vm \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys
        
    az vm extension set \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --vm-name my-vm \
        --name customScript \
        --publisher Microsoft.Azure.Extensions \
        --version 2.1 \
        --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
        --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
        
### Obtener IP y Almacenarlo en una Variable

    IPADDRESS="$(az vm list-ip-addresses \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --name my-vm \
        --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" \
        --output tsv)"

### Mostrar Grupos de Seguridad Asociados a la Máquina

    az network nsg list \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --query '[].name' \
        --output tsv

## Mostrar Reglas del Grupo de Seguridad

    az network nsg rule list \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --nsg-name my-vmNSG

    az network nsg rule list \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --nsg-name my-vmNSG \
        --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
        --output table

### Crear Reglas de Grupo de Seguridad

    az network nsg rule create \
        --resource-group learn-63db53bf-03a6-402e-b2ad-81e4f0c16b3e \
        --nsg-name my-vmNSG \
        --name allow-http \
        --protocol tcp \
        --priority 100 \
        --destination-port-range 80 \
        --access Allow
       
