# Quick Start - Implementación Rápida

## 🚀 Inicio Rápido (5 Pasos)

### 1. Login y Preparación (5 minutos)

```powershell
# Login a Azure
az login

# Ver suscripciones disponibles
az account list --output table

# Seleccionar suscripción
az account set --subscription "TU-SUBSCRIPTION-ID"
```

### 2. Crear Service Principal (2 minutos)

```powershell
# Crear SP para GitHub Actions
az ad sp create-for-rbac --name "sp-cjf-alz-deployment" `
    --role contributor `
    --scopes /subscriptions/TU-SUBSCRIPTION-ID `
    --sdk-auth

# ⚠️ GUARDA LA SALIDA JSON - la necesitarás
```

### 3. Configurar GitHub Secrets (3 minutos)

1. GitHub → Tu Repo → Settings → Secrets → Actions
2. Agregar:
   - `AZURE_CREDENTIALS`: Pega TODO el JSON del paso 2
   - `AZURE_SUBSCRIPTION_ID`: Tu Subscription ID
   - `AZURE_TENANT_ID`: Tu Tenant ID

### 4. Validar Localmente (Opcional pero Recomendado)

```powershell
cd "CJF-Azure-Migration\3.0-Manage-Optimize\3.1-Repeatable-Deployment\ALZ-Deployment\deployment-scripts"

# Solo validar (NO despliega)
.\deploy-alz.ps1 -SubscriptionId "TU-SUBSCRIPTION-ID" -ValidateOnly
```

### 5. Ejecutar Workflow en GitHub (30-60 minutos)

1. GitHub → Actions → "Deploy Azure Landing Zone (ALZ) - CJF"
2. Run workflow
3. Ingresar Subscription ID
4. Esperar a que complete

**¡Listo!** Los artifacts se generarán automáticamente.

---

## 📋 Comandos Esenciales

### Verificar Estado
```powershell
# Ver deployments
az deployment sub list --query "[?name=='alz-deployment-*']" --output table

# Ver resource groups
az group list --query "[?contains(name, 'cjf')]" --output table

# Ver recursos creados
az resource list --resource-group "rg-cjf-alz-deployment" --output table
```

### Ejecutar ALZ Review Manualmente
```powershell
# Instalar extensión
az extension add --name alz-review

# Ejecutar review
az alz review --subscription-id "TU-SUBSCRIPTION-ID" --output-file "alz-review.json"
```

### Limpiar (Si necesitas empezar de nuevo)
```powershell
# ⚠️ CUIDADO: Esto elimina TODO
az group delete --name "rg-cjf-alz-deployment" --yes --no-wait
```

---

## 🎯 Orden Recomendado de Ejecución

1. ✅ **Primero**: Validar localmente (`-ValidateOnly`)
2. ✅ **Segundo**: Ejecutar workflow en GitHub (solo validación)
3. ✅ **Tercero**: Ejecutar workflow completo (despliegue)
4. ✅ **Cuarto**: Revisar artifacts y guardarlos
5. ✅ **Quinto**: Repetir para Pemex

---

## ⚠️ Checklist Antes de Desplegar

- [ ] Tienes permisos de Owner/Contributor en la suscripción
- [ ] Service Principal creado y JSON guardado
- [ ] Secrets configurados en GitHub
- [ ] Validación local pasó exitosamente
- [ ] Tienes 30-60 minutos disponibles (tiempo de despliegue)
- [ ] Backup de información importante (si aplica)

---

## 🆘 Si Algo Sale Mal

1. **Revisa los logs del workflow** en GitHub Actions
2. **Verifica permisos**: `az role assignment list --assignee "TU-SP-ID"`
3. **Valida template manualmente**: `az deployment sub validate ...`
4. **Consulta la guía completa**: `GUIA_IMPLEMENTACION_PASO_A_PASO.md`

---

**Tiempo Total Estimado:** 1-2 horas (incluyendo tiempo de despliegue)

