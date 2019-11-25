# Otorgar permisos a Azure Data Factory para acceder a recursos como Azure Data Lake

Cuando estamos creando un pipeline en Azure Data Factory en el que queremos aplicar una acción Copy para copiar información desde un silo de origen (puedes ver aquí como vincular por ejemplo una base de datos SQL $$$) hacia un sink o destino, como podría ser un Azure Data Lake Storage, puede ocurrir que tengas el siguiente error de acceso y permisos.

>> [{"code":9083,"message":"Access to https://------.azuredatalakestore.net/webhdfs/v1/ is denied. Make sure the ACL and firewall rule is correctly configured in the Azure Data Lake Store account. Service request id: ------ Response details: {\"RemoteException\":{\"exception\":\"AccessControlException\",\"message\":\"Permission denied on / [------][2019-07-16T00:13:21.8941188-07:00]\",\"javaClassName\":\"org.apache.hadoop.security.AccessControlException\"}}","details":"{\"Code\":9083,\"Message\":\"Access to https://-------.azuredatalakestore.net/webhdfs/v1/ is denied. Make sure the ACL and firewall rule is correctly configured in the Azure Data Lake Store account. Service request id:------ Response details: {\\\"RemoteException\\\":{\\\"exception\\\":\\\"AccessControlException\\\",\\\"message\\\":\\\"Permission denied on / [-------][2019-07-16T00:13:21.8941188-07:00]\\\",\\\"javaClassName\\\":\\\"org.apache.hadoop.security.AccessControlException\\\"}}\",\"EventType\":0,\"Category\":5,\"Data\":{},\"MsgId\":null,\"ExceptionType\":\"Microsoft.DataTransfer.Common.Shared.HybridDeliveryException\",\"Source\":\"Microsoft.DataTransfer.ClientLibrary\",\"StackTrace\":\"\",\"InnerEventInfos\":[{\"Code\":0,\"Message\":\"'Type=,Message=The remote server returned an error: (403) Forbidden.,Source=,'\",\"EventType\":0,\"Category\":5,\"Data\":{},\"MsgId\":null,\"ExceptionType\":\"System.Net.WebException\",\"Source\":\"System\",\"StackTrace\":\"\",\"InnerEventInfos\":[]}]}"}]

Este error 403, Access denied significa que Azure Data Factory no dispone de permisos suficientes para acceder al recurso Azure Data Lake Storage.

Para solucionarlo, hagamos los siguiente.

## Obtener el identificador principal

En la pestaña de edición del servicio vinculado de destino (Azure Data Lake Storage Gen1) dentro de Azure Data Factory, indicaremos todos los parametros para identificar el recurso al que intentamos acceder, tales como URI de la cuenta, nombre del grupo de recurso, etc. Esto es realmente facil utilizando la opción "Data Lake Store selection method > From Azure subscription".

En Authentication type, escogeremos Managed Identity, y nos fijaremos en el identificador de aplicación que aparece debajo. En la imagen se puede ver en amarillo.

![edit linked service](./2019-07-16/edit-linked-service.jpg)

Copiamos ese identificador y vamos al panel de Azure, a la pantalla del recurso Azure Data Lake Storage que intentamos vincular.

## Gestionar permisos de acceso en Azure Data Lake

En el Portal de Azure, dentro del recurso Azure Data Lake Storage Gen1, vamos a la pestaña __"Access control (IAM)"__ desde donde podemos gestionar los permisos.

1. A continuación pulsamos en __"Add a role assignment > Add"__.
2. Selecciona el rol __Contributor__  e introduce el codigo de aplicación que copiaste en el paso anterior.
3. Selecciona en la lista el recurso de DataFactory que esta identificado por ese codigo.
4. Pulsa __Save__

![permission assigned](./2019-07-16/permission-assigned.jpg)

## Vuelve a Azure Data Factory y verifca el enlace

Si todo ha ido bien, antes de guardar la configuración en la pestaña de "Edit Linked Service" en Azure Data Factory, podrás ver el botón __"Test Connection"__, si lo usas, debería aparecer un tick verde indicando que ha podido acceder al recurso.


Espero que, si te ha ocurrido esto, ahora tengas una visión de qué hacer para solucionarlo.


