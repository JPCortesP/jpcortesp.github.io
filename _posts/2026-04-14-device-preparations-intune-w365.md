---
layout: post
title: "Device Preparations en Intune y Windows 365"
date: 2026-04-14 14:00:00 +0000
tags: [blog]
---

Device Preparations (El nuevo "Autopilot v2") es una de esas capacidades que vale la pena seguir de cerca si trabajas con Intune.

La promesa es bastante clara: simplificar la preparacion del dispositivo, reducir friccion en el aprovisionamiento y hacer mas consistente la experiencia inicial.

El problema es que, cuando lo combinamos con Windows 365, se vuelve un poco más confuso. Para dejar un poco más claro, veamos un poco los "momentos" de instalación de aplicaciones en Windows 365:

<!--more-->
## Momentos cuando las apps se instalan

Asumiento que las aplicaciones están creadas y asignadas en Intune previo a crear la CPC:

| Momento | Cuando | Manejo de fallos | Notas / Ejemplos |
| --- | --- | --- | --- |
| Imagen | Previo al aprovisionamiento | Si la imagen está bien, no habrán fallos, si la imagen no está bien, la máquina simplemente no se aprovisionará | M365 Apps (Office) como parte de la imagen de galería |
| Política de Apps de Intune **por usuario** | A partir del primer inicio de sesión, en cualquier momento | Reintentará indefinidamente y reportado por Intune | Apps en el blade Apps de Intune |
| Política de Apps de Intune **por dispositivo** | Luego de crear la máquina, previo al primer inicio de sesión | Falla del aprovisionamiento reportado, no muchos detalles. | n/a |
| Device Preparation **Automáticas** para W365 | Luego de crear la máquina, previo al primer inicio de sesión | Si algún app falla, y es requerida, se reporta como tal y se re-aprovisiona hasta que funcione, si no es requerida, o si se permite en el Deployment Policy, se permite el inicio de sesión y se seguirá intentando | Adicional, agrega la CPC al Security Group asignado de forma automática |

Entonces, lo más importante que salta acá es, ¿para que usaría Device Preparations si asignar apps por dispositivo es igual de útil?, y la respuesta va por temas de **perfilamiento de usuarios**.

## Perfilamiento de usuarios con Windows 365

Si generalmente solo tenemos un único perfil para todos los usuarios, entonces nada de esto es importante para nosotros; **sin embargo**, si tengo más de 2 perfiles de usuario, cada uno con una serie de requisitos diferentes de aplicaciones, y con otro grupo de aplicaciones que tienen que estar en **todas las máquinas, CPCs o no**, es entonces dónde los Device Preparations tienen sentido.

**Mi Recomendación por cada perfil de usuario:**

- Grupo de seguridad de usuarios, y si es W365 Enterprise, con auto-licensing. (eje, W365-Contabilidad )
- Provisioning Policy "W365 - Contabilidad" asignado al grupo anterior, y aplicando el Preparation Policy llamado "W365 - PrepPolicy - Contabilidad"
- Preparation Policy asignando las Apps requeridas (eje, contaApp1) y opcionalmente cualquier script requerido, y asignando las CPCs al grupo de seguridad "Devices-W365Contabilidad"
- El grupo de Devices "Devices - W365Contabilidad" con el owner asignado al Service Principal de Intune.

El flujo cada vez que agregamos un usuario al grupo de usuarios sería:

1. W365 reconoce el nuevo usuario con licencia, y aplica la configuración del Provisioning Policy y empieza a crear la CPC.
2. Durante la creacion, utiliza imagen, ubicación, red etc, del provisioning Policy asignado. Basado en la licencia asignada al usuario, se selecciona el tamaño de la máquina. 
4. W365 reconoce que la CPC tiene asignada un Preparation Policy, y aplica su configuración (instalación de apps y ejecución de scripts). La etiqueta cambia de "provisioning" a "preparing".
5. Device Preparation agrega la CPC al grupo de seguridad "Devices - W365Contabilidad"
6. Luego de que la CPC está creada, aplica la configuración de las Apps requeridas por dispositivo al unirse a Intune.
7. Luego/Durante el primer inicio de sesión, se aplican las políticas (apps incluidas) de usuario al CPC.

Entonces, si hablamos de Apps y políticas, los debería de aplicar:

- Apps de seguridad y políticas de linea base de seguridad que todo device debería de tener, como AV o EDR, se debería de aplicar a "All Devices", y si hay excepciones, se agregan como excepciones en esa misma asignación.
- Políticas de seguridad específicas para Windows 365 se puede usar "All devices" pero filtrado a W365. **Más sobre este tema luego.**
- Apps de perfil específicos, se puede agregar al probable grupo de seguridad de usuarios de "Usuarios - Contabilidad", y además lo agregamos en el grupo de dispositivos "Devices - W365Contabilidad". Además, se asignan las (max 10) más importantes en el Device Prep Policy.
- VPN y otras que son más específicas para grupos disjuntos de usuarios, se mantiene en Intune su asignación por grupo de usuarios.

Suena complicado, pero es "simple". En el siguiente post veremos cómo crear un nuevo perfil de usuario de Windows 365 de principio a fin, partiendo que tenemos licencia.
