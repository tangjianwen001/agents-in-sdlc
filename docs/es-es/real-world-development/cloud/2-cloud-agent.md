---
title: "Ejercicio 2 - Agente de GitHub Copilot en la nube"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Ejercicio anterior: Instrucciones personalizadas][previous-lesson] |
|:--|

Pocas organizaciones están libres de deuda técnica. Puede tratarse de problemas de seguridad pendientes, código antiguo que necesita actualizarse o funcionalidades del backlog que no se han implementado por falta de tiempo. El agente de GitHub Copilot en la nube puede actualizar código y añadir funcionalidades de forma autónoma. Cuando termina, genera una solicitud de incorporación de cambios (PR) en borrador para que la revise un desarrollador. Así se pueden delegar tareas tediosas y dedicar más tiempo a asuntos de mayor alcance.

En este ejercicio explorarás cómo:

- personalizar el entorno de generación de código.
- asegurarte de que las operaciones se realizan de forma segura.
- definir incidencias con un alcance claro.
- asignar incidencias a Copilot.

## Escenarios

Tailspin Toys quiere resolver parte de su deuda técnica. Los contratistas que crearon la primera versión del sitio dejaron el código sin documentación. Como primer paso, el equipo quiere añadir comentarios TSDoc a todas las funciones exportadas.

Además, el equipo de diseño quiere facilitar el descubrimiento de juegos. Quiere mostrar juegos relacionados —otros títulos de la misma categoría— en la página de detalles de cada juego para explorar la experiencia de usuario (UX, del inglés *user experience*) y hacer pruebas de aceptación. No hace falta que la implementación esté pulida: esta tarea bloquea el trabajo, pero hay incidencias con mayor prioridad.

Estas tareas pueden perder prioridad con facilidad, por lo que son buenas candidatas para el agente en la nube. El agente puede trabajar en ellas de forma asíncrona mientras el desarrollador se ocupa de otras tareas y, más tarde, vuelve para revisar el resultado.

## Presentación del agente de GitHub Copilot en la nube

El [agente de GitHub Copilot en la nube](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede trabajar en segundo plano de forma parecida a un desarrollador. Una de las opciones es [asignar una incidencia de GitHub a Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Tras la asignación, Copilot crea una PR en borrador para seguir el progreso, prepara el entorno y empieza a trabajar. Puedes examinar la sesión mientras está en curso o después de que termine. Cuando la solución está lista para revisión, Copilot te menciona en la PR.

## La importancia de definir bien el alcance

GitHub Copilot no elimina la necesidad de pensar. Incluso las tareas aparentemente sencillas pueden tener complejidades. Ten en cuenta las [recomendaciones para definir el alcance de las tareas del agente en la nube][cloud-agent-best-practices]. Trátalo como un compañero de programación con inteligencia artificial (IA): trabaja por etapas, aprende, experimenta y adapta el proceso. Los fundamentos del desarrollo de software no cambian al incorporar IA generativa.

## Instrucciones personalizadas de este repositorio

Antes de asignar trabajo al agente en la nube, revisa en modo de solo lectura los archivos de instrucciones incluidos en este repositorio. Así podrás reconocer su efecto más adelante.

1. En la interfaz web de GitHub o en un codespace, abre `.github/copilot-instructions.md` y revisa la sección **Code standards**, en especial las convenciones de TypeScript y los comentarios TSDoc.
2. Abre `.github/instructions/unit-tests.instructions.md` y observa el frontmatter `applyTo`, que limita estas instrucciones a los archivos `**/*.test.ts`.

Cuando asignes la incidencia `Code lacks documentation`, comprueba si la PR resultante incluye comentarios TSDoc, sigue las convenciones de TypeScript y contiene encabezados de comentario. Estos elementos proceden de los archivos de instrucciones.

## Configura el entorno de desarrollo del agente en la nube

El agente en la nube utiliza [GitHub Actions][github-actions] para preparar su entorno. Puedes personalizarlo con un [flujo de trabajo de configuración][setup-workflow] en `.github/workflows/copilot-setup-steps.yml`. Este permite instalar las herramientas y dependencias necesarias antes de iniciar el trabajo. El flujo ya está configurado para el taller con Node.js, las dependencias del proyecto, los navegadores de las pruebas de extremo a extremo y la base de datos SQLite migrada y poblada para la aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

Este flujo contiene algunos elementos importantes:

- Un único trabajo, `copilot-setup-steps`, que se ejecuta en GitHub Actions antes de que Copilot empiece a trabajar en la PR.
- El activador `workflow_dispatch`, que permite ejecutar el flujo manualmente desde la pestaña **Actions** para probarlo sin esperar a que Copilot lo inicie.

## Añade documentación

Muchos proyectos tienen documentación desactualizada o carecen de ella. Esta deuda técnica reduce la productividad y dificulta el mantenimiento y la incorporación de desarrolladores. Copilot puede ayudar a generar la documentación necesaria, por lo que esta es una buena incidencia para asignarle. Más adelante volverás a revisar su trabajo.

1. En una pestaña nueva del navegador, abre tu repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue**.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Code lacks documentation`.
6. En **Description**, escribe:

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. Selecciona **Create** para crear la incidencia.
8. En el panel de la derecha, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

   ![Captura de la asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

   ![Captura de los detalles de asignación de Copilot](../../../_images/ex4-assign-copilot-details.png)

10. Selecciona la pestaña **Pull Requests**.
11. Abre la PR generada, cuyo título será parecido a `[WIP]: Code lacks documentation`. Si todavía no aparece, espera un momento y actualiza la página.
12. Tras unos minutos, Copilot debería crear una lista de tareas.

> [!NOTE]
> Puede que la lista de tareas de Copilot tarde varios minutos en aparecer en la PR. Copilot está configurando el entorno (ejecutando el flujo de trabajo mencionado), analizando el proyecto y determinando el mejor enfoque.

13. Revisa la lista y las tareas que Copilot va a completar.
14. Desplázate por la cronología de la PR hasta encontrar la actualización que indica que Copilot ha empezado a trabajar.
15. Selecciona **View session** para examinar la sesión en directo.

   ![Vista de sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Puede que tengas que actualizar la página para ver el indicador.

16. Observa la sesión en directo y cómo Copilot resuelve el problema: explora el código, analiza el estado, decide un plan y crea código.

El proceso puede tardar varios minutos. Uno de los objetivos principales del agente de Copilot en la nube es trabajar de forma asíncrona para que puedas ocuparte de otras tareas. Aprovecharás esta función asignándole otra tarea mientras trabajas en una funcionalidad de la aplicación.

## Añade una sección de juegos relacionados

El agente en la nube permite repartir el trabajo y ocuparse de distintas tareas en paralelo. Asígnale la creación de una sección de juegos relacionados para el equipo de diseño.

1. Vuelve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue**.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Show related games on the game details page`.
6. En **Description**, escribe:

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. Selecciona **Create** para crear la incidencia.
8. En el panel derecho, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

   ![Captura de la asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Poco después aparecerá un emoji 👀 en el primer comentario de la incidencia para indicar que Copilot está trabajando.

![El emoji de ojos indica que Copilot está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

El agente trabajará en esta segunda solicitud en segundo plano. No es necesario supervisar cada paso; revisa el resultado cuando termine. Mientras tanto, continúa con la creación y el uso de agentes personalizados.

## Resumen y siguientes pasos

Has explorado el [agente de GitHub Copilot en la nube][copilot-agents]. Puedes asignarle incidencias para que trabaje de forma asíncrona, resuelva deuda técnica, cree funcionalidades o ayude a migrar código entre frameworks.

En este ejercicio:

- has personalizado el entorno de generación de código.
- has considerado cómo se realizan las operaciones de forma segura.
- has aprendido la importancia de definir incidencias con un alcance claro.
- has asignado incidencias a Copilot.

A continuación, crearás y utilizarás agentes personalizados. El agente de Copilot en la nube también puede utilizar servidores del Model Context Protocol (MCP) y archivos de instrucciones personalizadas, que ya has explorado en ejercicios anteriores.

## Recursos

- [Acerca del agente de Copilot en la nube][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Buenas prácticas para el flujo de configuración del agente en la nube][cloud-agent-best-practices]

| [Siguiente ejercicio: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
