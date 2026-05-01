---
layout: post
title: "Cloud PC move en Windows 365: cuándo sí, cuándo no y cuándo no se puede"
date: 2026-04-30 15:00:00 +0000
tags: [blog, windows365]
---
Si administras Cloud PCs (CPCs), tarde o temprano aparece esta necesidad: ese usuario (o grupo) ahora trabaja en otra red y quieres mover su CPC sin reprovisionar desde cero.

Ahí entra **Cloud PC Move**.

<!--more-->

## ¿Qué resuelve Cloud PC Move?

Cloud PC Move permite cambiar una CPC desde su red actual a otra red objetivo (normalmente asociada a otro Azure Network Connection) cuando el contexto del usuario cambió. También sirve para cambiar de región en los casos donde se usa Red Provista por Microsoft (Microsoft Hosted Network), pero no profundizaremos en este caso.

Según Microsoft, este cambio se hace editando el provisioning policy y aplicándolo a algunas o todas las CPCs existentes. Importante: mover una CPC **no** es reprovisionarla, pero durante el movimiento puede estar apagada e inaccesible por **varias horas**.

Piensa en escenarios como:

- Cambios de sede o país para equipos permanentes.
- Integraciones/M&A donde cambian los segmentos de red corporativos.
- Ajustes de arquitectura para acercar la CPC a apps o servicios internos.

El objetivo no es "optimizar por probar", sino alinear la CPC con una nueva realidad operativa de red.

## Escenarios soportados

Estos son escenarios que sí aparecen como soportados:

- Microsoft Entra Join: Microsoft-hosted network (región/grupo) hacia otra Microsoft-hosted network.
- Microsoft Entra Join: ANC hacia otro ANC.
- Microsoft Entra Join: Microsoft-hosted network hacia ANC (y viceversa).
- Microsoft Entra Hybrid Join: ANC hacia otro ANC (manteniendo Hybrid Join).

Además, para mover un subconjunto, se pueden seleccionar hasta 100 dispositivos por operación.

## Cuándo sí tiene sentido

En mi experiencia, sí tiene sentido cuando el cambio es **estructural** y no temporal.

Casos típicos:

- El usuario cambió de unidad o región y ese cambio será estable.
- Necesitas que la CPC use otro camino de conectividad hacia recursos corporativos.
- Quieres estandarizar un grupo completo en una misma red de destino.

Una regla práctica: si ibas a intentar un reprovisionamiento por cambio de red, probablemente Move Network sea mejor alternativa.

## Cuándo no tiene sentido

No todo problema de experiencia o rendimiento se arregla moviendo red; en la práctica, casi ninguno, de hecho.

Suele **no** tener sentido cuando:

- El problema real es de capacidad (SKU/tamaño) y no de red.
- El problema es de políticas, apps o baseline de seguridad en Intune.
- El cambio de ubicación del usuario es temporal (proyecto corto, viaje, soporte puntual).
- Solo buscas "probar" si mejora latencia sin una hipótesis técnica clara.

En esos casos, es mejor validar primero métricas de conectividad, salud del endpoint, tamaño de CPC y configuración de políticas.

## Cuándo no se puede (o no deberías asumir que se puede)

Aquí está la parte importante: Move Network no es una varita mágica para cualquier escenario.

### Casos específicos no soportados (incluye AD)

Este punto era clave: **no puedes usar Move Network para unir o sacar una CPC de AD**.

Traducción práctica:

- No soporta cambiar de **Hybrid Microsoft Entra Join** (unida a AD on-prem vía híbrido) a **Microsoft Entra Join**.
- No soporta cambiar de **Microsoft Entra Join** a **Hybrid Microsoft Entra Join**.

O sea, si tu objetivo es "pasar esta CPC dentro/fuera de AD", Move Network no es el mecanismo.

También considera estas limitaciones oficiales:

- No se pueden mover CPCs de un provisioning policy a otro.
- No se puede, en una sola edición, mover parte de las CPCs a una región y otra parte a otra región distinta.
- No se cambia VNet/subnet directamente con el edit del policy; para eso hay que crear un ANC nuevo con la VNet/subnet deseada y mover hacia ese ANC.

En general, puedes encontrarte con limitaciones por:

- Requisitos de elegibilidad de la CPC o del tipo de provisión.
- Estado de la máquina (por ejemplo, operaciones pendientes o estados no compatibles).
- Configuración de la red de destino que no cumple prerequisitos.
- Restricciones específicas del entorno/licenciamiento o de la arquitectura actual.

En otras palabras: antes de planificar un movimiento masivo, valida elegibilidad técnica en un piloto pequeño.

## Recomendación operativa

Si tienes que mover "un particular o un grupo" de CPCs a otra red, haría este orden:

1. Confirmar que el motivo es realmente de red y no de capacidad/política.
2. Definir grupo piloto y red objetivo con criterios claros de éxito.
3. Ejecutar move en piloto y validar apps, acceso a recursos internos y experiencia de usuario.
4. Escalar por olas (waves), no todo de una vez.
5. Documentar casos que no califican y su alternativa (resize, reprovision o remediación de políticas).

Tip práctico: como el movimiento puede dejar CPCs inaccesibles por horas, programa ventanas fuera de horario laboral y avisa previamente a usuarios.

## Enlaces oficiales

- Cloud PC move (escenarios soportados, límites y mejores prácticas): [https://learn.microsoft.com/en-us/windows-365/enterprise/move-cloud-pc](https://learn.microsoft.com/en-us/windows-365/enterprise/move-cloud-pc)
- Sección de escenarios soportados: [https://learn.microsoft.com/en-us/windows-365/enterprise/move-cloud-pc#supported-cloud-pc-move-scenarios](https://learn.microsoft.com/en-us/windows-365/enterprise/move-cloud-pc#supported-cloud-pc-move-scenarios)
- Crear Azure Network Connection (requisitos de ANC y AD para híbrido): [https://learn.microsoft.com/en-us/windows-365/enterprise/create-azure-network-connection](https://learn.microsoft.com/en-us/windows-365/enterprise/create-azure-network-connection)
- Requisitos de red para Windows 365: [https://learn.microsoft.com/en-us/windows-365/enterprise/requirements-network](https://learn.microsoft.com/en-us/windows-365/enterprise/requirements-network)
- Reporte de acciones de Cloud PC (seguimiento del move): [https://learn.microsoft.com/en-us/windows-365/enterprise/report-cloud-pc-actions](https://learn.microsoft.com/en-us/windows-365/enterprise/report-cloud-pc-actions)

## Resumen rápido

- **Sí**: cuando el cambio de red es real, estable y con justificación técnica.
- **No**: cuando el problema está en otro lado (capacidad, políticas, configuración).
- **No se puede**: en escenarios no elegibles o con prerequisitos incumplidos.

Si quieren, en el siguiente post puedo dejar un checklist de pre-validación para usar Move Network sin sorpresas.