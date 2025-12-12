# Configuración de Private DNS Zones para ALZ - CJF

## Estado Actual de la Configuración

**Parámetro en ALZ-Parameters.json:**
```json
"enablePrivateDnsZones": {
  "value": "Yes"
},
"privateDnsZonesToDeploy": {
  "value": []
}
```

## Análisis

Con la configuración actual:
- `enablePrivateDnsZones: "Yes"` - La funcionalidad está habilitada
- `privateDnsZonesToDeploy: []` - **Array vacío significa que NO se despliegan zonas DNS privadas**

**Conclusión:** Aunque Private DNS Zones está habilitado, al tener el array vacío, **NO se deberían haber creado Private DNS Zones** en el despliegue.

## Private DNS Zones que se Deberían Desplegar

Si se desea desplegar Private DNS Zones, se deben especificar en el array `privateDnsZonesToDeploy`. A continuación se listan las zonas más comunes para servicios de Azure con Private Link:

### Zonas DNS Privadas Comunes para Azure Services

#### Storage Services
- `privatelink.blob.core.windows.net` - Azure Blob Storage
- `privatelink.file.core.windows.net` - Azure Files
- `privatelink.queue.core.windows.net` - Azure Queue Storage
- `privatelink.table.core.windows.net` - Azure Table Storage
- `privatelink.dfs.core.windows.net` - Azure Data Lake Storage Gen2

#### Database Services
- `privatelink.database.windows.net` - Azure SQL Database
- `privatelink.sql.azuresynapse.net` - Azure Synapse Analytics
- `privatelink.documents.azure.com` - Azure Cosmos DB
- `privatelink.mysql.database.azure.com` - Azure Database for MySQL
- `privatelink.postgres.database.azure.com` - Azure Database for PostgreSQL
- `privatelink.mariadb.database.azure.com` - Azure Database for MariaDB

#### Security and Identity
- `privatelink.vaultcore.azure.net` - Azure Key Vault
- `privatelink.azurecr.io` - Azure Container Registry

#### Compute and Networking
- `privatelink.azurewebsites.net` - Azure App Service / Web Apps
- `privatelink.<region>.azmk8s.io` - Azure Kubernetes Service (AKS) - Requiere región específica
- `privatelink.servicebus.windows.net` - Azure Service Bus
- `privatelink.eventgrid.azure.net` - Azure Event Grid

#### Backup and Recovery
- `privatelink.<region>.backup.windowsazure.com` - Azure Backup (requiere región específica)
  - Para West US 2: `privatelink.wus2.backup.windowsazure.com`

#### Analytics and AI
- `privatelink.azuredatabricks.net` - Azure Databricks
- `privatelink.openai.azure.com` - Azure OpenAI Service

#### Integration Services
- `privatelink.api.azureml.ms` - Azure Machine Learning
- `privatelink.cognitiveservices.azure.com` - Azure Cognitive Services

## Configuración Recomendada para CJF

Basado en los servicios típicos de una Azure Landing Zone, se recomienda desplegar las siguientes zonas:

```json
"privateDnsZonesToDeploy": {
  "value": [
    "privatelink.blob.core.windows.net",
    "privatelink.file.core.windows.net",
    "privatelink.queue.core.windows.net",
    "privatelink.table.core.windows.net",
    "privatelink.dfs.core.windows.net",
    "privatelink.database.windows.net",
    "privatelink.documents.azure.com",
    "privatelink.vaultcore.azure.net",
    "privatelink.azurewebsites.net",
    "privatelink.servicebus.windows.net",
    "privatelink.eventgrid.azure.net",
    "privatelink.wus2.backup.windowsazure.com"
  ]
}
```

## Verificación en Azure Portal

Para verificar qué Private DNS Zones se desplegaron (si alguna):

1. **Azure Portal** → **Private DNS zones**
2. Filtrar por Resource Group: `CJF-privatedns` o `CJF-privatedns-02`
3. Verificar la lista de zonas creadas

## Nota Importante

Si el despliegue se realizó con `privateDnsZonesToDeploy: []`, entonces:
- **NO se deberían haber creado Private DNS Zones**
- El Resource Group `CJF-privatedns` podría existir pero estar vacío
- O el Resource Group podría no haberse creado si no hay recursos

Para confirmar, verificar en Azure Portal si existen Private DNS Zones en los Resource Groups de Private DNS.

## Próximos Pasos

1. **Verificar en Azure Portal** qué Private DNS Zones existen actualmente
2. **Decidir qué zonas son necesarias** según los servicios que se van a usar
3. **Actualizar el parámetro** `privateDnsZonesToDeploy` con las zonas requeridas
4. **Re-desplegar** o crear las zonas manualmente si es necesario

---

**Última Actualización:** Diciembre 2025

