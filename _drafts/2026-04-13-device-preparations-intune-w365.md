---
layout: post
title: "Device Preparations en Intune y Windows 365"
date: 2026-04-13 14:00:00 +0000
tags: [blog]
---
# Device Preparations en Intune y Windows 365

Device Preparations (El nuevo "Autopilot v2") es una de esas capacidades que vale la pena seguir de cerca si trabajas con Intune.

La promesa es bastante clara: simplificar la preparacion del dispositivo, reducir friccion en el aprovisionamiento y hacer mas consistente la experiencia inicial.

El problema es que, cuando lo combinamos con Windows 365, se vuelve un poco más confuso. Para dejar un poco más claro, veamos un poco los "momentos" de instalación de aplicaciones en Windows 365:

<!--more-->
## Momentos cuando las apps se instalan

Asumiento que las aplicaciones están creadas y asignadas en Intune previo a crear la CPC:

| Momento | Cuando | Manejo de fallos | Notas / Ejemplos |
|---|---|---|----|
| Imagen | Previo al aprovisionamiento | Si la imagen está bien, no habrán fallos, si la imagen no está bien, la máquina simplemente no se aprovisionará | M365 Apps (Office) como parte de la imagen de galería |
| Política de Apps de Intune **por usuario** | A partir del primer inicio de sesión, en cualquier momento | Reintentará indefinidamente y reportado por Intune | Apps en el blade Apps de Intune |
| Política de Apps de Intune **por dispositivo** | Luego de crear la máquina, previo al primer inicio de sesión | Falla del aprovisionamiento reportado, no muchos detalles. | n/a |
| Device Preparation **Automáticas** para W365 | Luego de crear la máquina, previo al primer inicio de sesión | Si algún app falla, y es requerida, se reporta como tal y se re-aprovisiona hasta que funcione, si no es requerida, o si se permite en el Deployment Policy, se permite el inicio de sesión y se seguirá intentando | Adicional, agrega la CPC al Security Group asignado de forma automática |

Entonces, lo más importante que salta acá es, ¿para que usaría Device Preparations si asignar apps por dispositivo es igual de útil?, y la respuesta va por temas de **perfilamiento de usuarios**.

## Perfilamiento de usuarios con Windows 365

Si solo generalmente tenemos un único perfil para todos los usuarios, entonces nada de esto es importante para nosotros; **sin embargo**, si tengo más de 2 perfiles de usuario, cada uno con una serie de requisitos diferentes de aplicaciones, y con otro grupo de aplicaciones que tienen que estar en **todas las máquinas, CPCs o no**, es entonces dónde los Device Preparations tienen sentido.

