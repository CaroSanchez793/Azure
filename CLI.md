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
