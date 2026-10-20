---
title: "Ejercicio 4 - Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los ejercicios anteriores, has asignado al agente de Copilot en la nube tres tareas relacionadas con la mejora de la experiencia de usuario y la incorporación de funcionalidades. Aunque el agente trabaja de forma asíncrona y autónoma, sigue siendo importante supervisar esas tareas.

Hay varias herramientas para gestionar las tareas asignadas al agente, incluida la [página de agentes][agents-page] de GitHub.com. Desde este centro de control puedes ver todas las tareas con solicitudes de incorporación de cambios (PR) abiertas, examinar las operaciones realizadas y orientar una sesión en curso.

En este ejercicio:

- explorarás la página de agentes para supervisar las tareas del agente en la nube.
- orientarás una sesión en curso para solicitar otra funcionalidad.

## Escenario

Después de asignar al agente la creación del modo de alto contraste, el equipo ha decidido añadir también un modo claro. Como el agente ya está modificando los estilos del sitio y añadiendo controles de cambio de modo, le pedirás que incluya esta nueva funcionalidad.

## Revisa las tareas del agente de Copilot en la nube

Consulta el estado de las tareas asignadas al agente de Copilot en la nube.

1. Abre la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Examina la lista de tareas del panel principal y del panel izquierdo. Entre las tareas asignadas deberían aparecer:
   - actualizar la documentación de la base de código.
   - generar una interfaz de programación de aplicaciones (API) para modificar juegos.
   - añadir un modo de alto contraste al sitio web.
3. Selecciona una tarea en curso y revisa las operaciones que ha realizado Copilot. Pueden incluir:
   - obtener el código del repositorio.
   - crear el entorno de trabajo.
   - configurar servidores del Model Context Protocol (MCP).
   - completar varios pasos de la tarea.

> [!NOTE]
> Los pasos exactos varían según el estado del trabajo de Copilot y el enfoque elegido.

4. Observa el panel de PR que aparece a la derecha. Permite consultar la PR y los archivos modificados.

## Orienta al agente en la nube

Ahora que has visto las tareas activas, pide a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con el modo de alto contraste. El título exacto dependerá del nombre que use Copilot y del estado del trabajo.

   ![Sesión de accesibilidad en Mission Control](../../../_images/ex6-accessibility-session.png)

2. Espera unos minutos hasta que la sesión indique que ha terminado la configuración y ha empezado a trabajar.
3. En el cuadro **Steer active session while Copilot is working**, introduce este prompt:

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Captura de la tarea del agente en la página de agentes, con el cuadro para orientar la sesión activa resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar el prompt.
5. Observa cómo Copilot lo confirma y lo incorpora al flujo de trabajo.

## Deja que Copilot trabaje

Copilot incorporará la solicitud nueva cuando termine el paso que estaba ejecutando al recibir el mensaje. El proceso puede tardar varios minutos.

## Resumen y siguientes pasos

Has explorado la página de agentes de Copilot, el centro para supervisar y orientar las tareas del agente en la nube. Permite seguir las tareas activas y completadas, revisar el trabajo y redirigir una sesión en curso para ajustar su alcance o proporcionar indicaciones adicionales.

En este ejercicio:

- has explorado Mission Control y la página de agentes para supervisar las tareas del agente en la nube.
- has redirigido una sesión en curso para solicitar una funcionalidad adicional.

Mientras Copilot termina las funciones de accesibilidad, pasarás a [iterar sobre las solicitudes de incorporación de cambios que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Ejercicio anterior: Agentes personalizados][previous-lesson] | [Siguiente ejercicio: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
