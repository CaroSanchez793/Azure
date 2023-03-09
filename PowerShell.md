# PowerShell

## Instalación de PowerShell

Para **verificar la versión** de PowerShell que está instalada en el equipo se utiliza el siguiente comando:

    $PSVersionTable.PSVersion

En el caso de necesitar una **actualización**, se usan los siguientes comandos:

    winget search Microsoft.PowerShell

    winget install --id Microsoft.Powershell --source winget
    winget install --id Microsoft.Powershell.Preview --source winget

Tras la **instalación de SDK de .NET**, se ejecuta el siguiente comando:

    dotnet tool install --global PowerShell

## Instalación de Módulos

Para instalar y actualizar los módulos, se ejecutan los siguientes comandos:

    Install-Module -Name Az -Scope CurrentUser -Repository PSGallery

    Update-Module -Name Az

Siempre que sea posible, instale todos los módulos en una ruta de acceso que aparece en la variable de entorno **PSModulePath** o agregue la ruta de acceso del módulo al valor de la variable de entorno PSModulePath.

La variable de entorno PSModulePath ( ) contiene las ubicaciones de Windows PowerShell *$Env:PSModulePath* módulos. Los cmdlets se basan en el valor de esta variable de entorno para buscar módulos.

De forma predeterminada, el valor de la variable de entorno PSModulePath contiene los siguientes directorios del módulo del sistema y del usuario, pero puede realizar más incorporaciones al valor y editarlo.

    $PSHome\Modules (%Windir%\System32\WindowsPowerShell\v1.0\Modules)

    $Home\Documents\WindowsPowerShell\Modules (%UserProfile%\Documents\WindowsPowerShell\Modules)

    $Env:ProgramFiles\WindowsPowerShell\Modules (%ProgramFiles%\WindowsPowerShell\Modules)

Para obtener el valor de la variable de entorno PSModulePath, use cualquiera de los siguientes comandos.

    $Env:PSModulePath
    [Environment]::GetEnvironmentVariable("PSModulePath")

Para **agregar una ruta de acceso de módulo** al valor de la variable de entorno PSModulePath, utilice el siguiente formato de comando. Este formato utiliza el método *SetEnvironmentVariable* de la clase System.Environment para hacer un cambio independiente de la sesión en la variable de entorno PSModulePath.

    #Save the current value in the $p variable.
    $p = [Environment]::GetEnvironmentVariable("PSModulePath")

    #Add the new path to the $p variable. Begin with a semi-colon separator.
    $p += ";C:\Program Files (x86)\MyCompany\Modules\"

    #Add the paths in $p to the PSModulePath value.
    [Environment]::SetEnvironmentVariable("PSModulePath",$p)

Si crea su propio módulo u obtiene un módulo de otra parte, como un sitio web de la comunidad de Windows PowerShell, y desea que dicho módulo esté disponible solo para su cuenta de usuario, instálelo en el directorio de módulos específico del usuario.

    $home\Documents\WindowsPowerShell\Modules\<Module Folder>\<Module Files>

De forma predeterminada, el directorio de módulos específico del usuario se agrega al valor de la variable de entorno PSModulePath.

Si desea que un módulo esté disponible para todas las cuentas de usuario del equipo, instálelo en la ubicación Archivos de programa.

    $Env:ProgramFiles\WindowsPowerShell\Modules\<Module Folder>\<Module Files>

Si va a distribuir el módulo a otras partes, use la ubicación predeterminada Archivos de programa descrita anteriormente, o bien cree su propio subdirectorio específico de la empresa o específico del producto del directorio %ProgramFiles%.

Para habilitar las características de detección de módulos de Windows PowerShell para encontrar el módulo Fabrikam, el instalador del módulo Fabrikam agrega la ubicación del módulo al valor de la variable de entorno PSModulePath.

    $p = [Environment]::GetEnvironmentVariable("PSModulePath")
    $p += ";C:\Program Files\Fabrikam Technologies\Fabrikam Manager\Modules\"
    [Environment]::SetEnvironmentVariable("PSModulePath",$p)

Si un módulo se usa en varios componentes de un producto o en varias versiones de un producto, instale el módulo en un subdirectorio específico del módulo del subdirectorio %ProgramFiles%\Common Files\Modules.

En el ejemplo siguiente, el módulo Fabrikam se instala en un subdirectorio Fabrikam del subdirectorio %ProgramFiles%\Common Files\Modules. Tenga en cuenta que cada módulo reside en su propio subdirectorio en el subdirectorio Modules.

## Creación de Grupos de Recursos

1. Importar los cmdlets de Azure.

2. Conectarse a la suscripción de Azure.

        Connect-AzAccount

        Get-AzContext

        Get-AzSubscription

        Set-AzContext -Subscription '00000000-0000-0000-0000-000000000000'

        Get-AzResourceGroup

        Get-AzResourceGroup | Format-Table

3. Crear el grupo de recursos.

        New-AzResourceGroup -Name <name> -Location <location>

4. Compruebe que la creación se ha realizado correctamente.

        Get-AzResource | Format-Table

        Get-AzResource -ResourceGroupName ExerciseResources

        Remove-AzResourceGroup -Name MyResourceGroupName

## Creación De Una Máquina Virtual

    New-AzVm
       -ResourceGroupName <resource group name>
       -Name <machine name>
       -Credential <credentials object>
       -Location <location>
       -Image <image name>

Como alternativa, puede usar otros cmdlets para configurar la máquina virtual, como **Set-AzVMOperatingSystem**, **Set-AzVMSourceImage**, **Add-AzVMNetworkInterface** y **Set-AzVMOSDisk**.

    New-AzVM -Name MyVm -ResourceGroupName ExerciseResources -Credential (Get-Credential)

|Comando|Descripción|
|--|--|
|**Remove-AzVM**|Elimina una máquina virtual de Azure.|
|**Start-AzVM**|Iniciar una máquina virtual detenida.|
|**Stop-AzVM**|Detener una máquina virtual en ejecución.|
|**Restart-AzVM**|Reiniciar una máquina virtual.|
|**Update-AzVM**|Actualiza la configuración de una máquina virtual.|

Puede enumerar las máquinas virtuales de la suscripción con el comando **Get-AzVM -Status**. Este comando también admite la especificación de una máquina virtual específica mediante la inclusión de la propiedad *-Name*.

    $vm = Get-AzVM  -Name MyVM -ResourceGroupName ExerciseResources

Lo interesante es que ahora la máquina virtual es un objeto con el que puede interactuar. Por ejemplo, puede realizar cambios en ese objeto y, después, devolverlos a Azure con el comando **Update-AzVM**:

    $ResourceGroupName = "ExerciseResources"

    $vm = Get-AzVM  -Name MyVM -ResourceGroupName $ResourceGroupName
    
    $vm.HardwareProfile.vmSize = "Standard_DS3_v2"

    Update-AzVM -ResourceGroupName $ResourceGroupName  -VM $vm

Para conectarse a la máquina creada desde PowerShell mediante la conexión ssh:

    ssh usuario@direcciónIP

Para eliminar una máquina virtual, primero es necesario detenerla:

    Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName

    Remove-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName

    Get-AzResource -ResourceGroupName $vm.ResourceGroupName | Format-Table

Es necesario eliminar todos los recursos que se crear con la máquina virtual y que se mantienen como redes o discos virtuales.

    $vm | Remove-AzNetworkInterface –Force

    Get-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $vm.StorageProfile.OSDisk.Name | Remove-AzDisk -Force

    Get-AzVirtualNetwork -ResourceGroupName $vm.ResourceGroupName | Remove-AzVirtualNetwork -Force

    Get-AzNetworkSecurityGroup -ResourceGroupName $vm.ResourceGroupName | Remove-AzNetworkSecurityGroup -Force

    Get-AzPublicIpAddress -ResourceGroupName $vm.ResourceGroupName | Remove-AzPublicIpAddress -Force

## Ejemplo Práctico

Conectarse a la cuenta de Azure y obtener la suscripción correspondiente.

    Connect-AzAccount

    Get-AzSubscription

    Set-AzContext -Subscription '00000000-0000-0000-0000-000000000000'

    Get-AzResourceGroup

Crear un grupo de recursos.

    New-AzResourceGroup -Name 'myResourceGroup' -Location 'EastUS'

Crear una máquina virtual en el grupo de recursos.
    
    New-AzVm
       -ResourceGroupName 'myResourceGroup' 
       -Name 'VirtualMachineProduction'
       -Credential (Get-Credential)
       -Location 'EastUs'
       -VirtualNetworkName 'VPC01'
       -SubnetName 'vpc-privatesubnet-01'
       -SecurityGroupName 'ProductionSecurityGroup'
