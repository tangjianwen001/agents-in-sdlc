---
title: "Ejercicio 2 - Añadir funcionalidad con el modo de agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso los cambios más sencillos suelen requerir actualizar varios archivos y ejecutar tareas, como las pruebas. El flujo habitual de desarrollo puede incluir localizar los archivos necesarios, realizar cambios, ejecutar pruebas, depurar, descubrir que falta otro archivo y volver a modificarlo.

El modo de agente de Copilot puede ayudar con este proceso. Trabaja de forma más autónoma en el IDE: explora la estructura del proyecto, realiza los cambios necesarios, ejecuta tareas como las pruebas y corrige los errores que detecta. En este ejercicio, utilizarás el modo de agente para añadir una funcionalidad al sitio.

> [!NOTE]
> Aunque los nombres se parecen, el modo de agente y el agente en la nube ofrecen experiencias distintas. El modo de agente realiza tareas en el IDE y permite obtener comentarios con rapidez. El agente en la nube trabaja de forma asíncrona, como otro miembro del equipo, y se comunica contigo mediante incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás cómo:

- el modo de agente de Copilot explora el proyecto, localiza archivos pertinentes y coordina cambios.
- el modo de agente de GitHub Copilot implementa funcionalidades en la interfaz y la capa de datos.
- revisar los cambios y las pruebas generados antes de combinarlos en la base de código.

## Escenario

A medida que crece la lista de juegos, quieres permitir que los usuarios filtren por categoría y editorial. En el ejercicio anterior añadiste un helper de editoriales; ahora completarás el trabajo restante de la capa de datos, la interfaz y las pruebas con el modo de agente de Copilot.

## Ejecuta el sitio web de Tailspin Toys

Antes de realizar cambios, explora el sitio web para conocer sus funciones actuales.

El sitio es una plataforma de financiación colectiva de juegos de mesa con temática de desarrollo. Permite mostrar juegos y consultar sus detalles. Se trata de una única aplicación Astro que genera páginas de lenguaje de marcado de hipertexto (HTML) estáticas durante la compilación. Las páginas consultan directamente una base de datos SQLite local desde el frontmatter mediante Drizzle ORM (mapeo objeto-relacional); no hay una interfaz de programación de aplicaciones (API) de backend separada ni un framework de interfaz del lado del cliente. Los helpers reutilizables de acceso a datos están en `src/lib/`, y la interactividad se añade con un elemento `<script>` de Astro acotado que utiliza las API estándar del modelo de objetos del documento (DOM).

### Inicia el sitio web

El comando `npm run dev` inicia el único servidor de desarrollo de Astro. Ejecútalo desde el Codespace:

1. Vuelve a tu codespace. Después del ejercicio anterior, deberías estar en `main`.
2. Abre un terminal en el Codespace con <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Crea una rama para el trabajo de filtrado:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Inicia el sitio:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo Astro esté listo, aparecerá un mensaje parecido a este:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo que te pide abrir `http://localhost:4321` en el navegador, ciérralo seleccionando **x**.

5. Abre el sitio manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando `http://localhost:4321` en el terminal.

> [!NOTE]
> En un codespace, el enlace local se redirige automáticamente a una dirección como `https://<your-codespace-name>-4321.app.github.dev/`. Se trata de un túnel privado al servidor web que se ejecuta en el codespace.

### Explora el sitio web

Cuando el sitio esté en ejecución, examina las funciones principales:

- **Página de inicio**: muestra una lista de juegos de mesa con títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre una página con más información, como el título, la descripción, la editorial y la categoría.

## Explora el backlog con Copilot

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar los ejercicios siguientes, vuelve al codespace, abre el panel Copilot Chat y selecciona **New Chat** para iniciar una conversación limpia. El modo y el modelo varían en cada ejercicio; los pasos correspondientes lo especifican.

La versión inicial del sitio funciona, pero queremos añadir capacidades. Primero revisarás el backlog, que se creó automáticamente al preparar el proyecto a partir de la plantilla.

1. En el menú desplegable de agentes de la vista Chat, selecciona **Agent**. El agente planifica e implementa cambios en varios archivos, ejecuta comandos del terminal e invoca herramientas.

   ![Captura del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. Selecciona **Claude Sonnet 4.5** en la lista de modelos disponibles.

> [!CAUTION]
> Los autores del taller no expresan preferencia por un modelo. Se utilizó Claude Sonnet 4.5 al crear el taller y se indica aquí para obtener sugerencias relativamente coherentes y una experiencia uniforme. Como los modelos de lenguaje grandes (LLM, del inglés *large language model*) son probabilísticos, las sugerencias pueden diferir de los ejemplos. Es normal.

> [!NOTE]
> Debido a la naturaleza probabilística de los LLM, Copilot puede utilizar un comando MCP distinto y aun así completar la tarea.

3. Envía este prompt a Copilot para consultar el backlog:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecciona **Continue** para ejecutar el comando que enumera las incidencias.
5. Revisa la lista generada.

Observa cómo Copilot ha priorizado los elementos según su posible utilidad para los usuarios.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, revisa el archivo de instrucciones que aportará contexto. Utilizarás el archivo de [interfaz de usuario (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md), que describe cómo añadir funcionalidades al sitio.

1. En tu codespace, abre `.github/instructions/ui.instructions.md`.
2. Observa las directrices generales para añadir funcionalidades. Incluyen:
   - una descripción de la arquitectura.
   - principios para el diseño de componentes, las pruebas y la accesibilidad.
   - enlaces a instrucciones específicas para Astro, Tailwind CSS y Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden enlazar a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas a la ubicación del archivo de instrucciones. Esto permite reutilizar y dividir directrices complejas, además de proporcionar ejemplos y plantillas.

## Implementa el filtrado

Para completar el filtrado, tendrás que realizar al menos tres cambios en la aplicación:

- añadir o ajustar la lógica de filtrado restante en la capa de datos (`src/lib/`).
- añadir o actualizar pruebas del comportamiento del filtrado.
- actualizar la página de lista de juegos para incluir la interfaz de filtrado.

Antes de combinar los cambios, también tendrás que ejecutar las pruebas y confirmar que pasan. El modo de agente de Copilot puede realizar estas tareas.

1. Continúa la conversación actual con Copilot o selecciona **New Chat** para iniciar otra.
2. Selecciona **Add Context**, **Instructions** y **ui** para añadir el archivo de instrucciones.

   ![Captura de la selección del archivo de instrucciones de interfaz](../../../_images/ex3-select-instructions-file.png)

3. Confirma que **Agent** sigue seleccionado en el menú desplegable de agentes de la vista Chat.
4. Confirma que el modelo seleccionado sigue siendo **Claude Sonnet 4.5**.
5. Envía este prompt para que Copilot implemente la funcionalidad a partir de la incidencia relacionada del backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Observa cómo Copilot explora el proyecto y localiza los archivos pertinentes de la capa de datos, la interfaz y las pruebas. Después, modificará archivos y ejecutará pruebas.

   ![Captura de Copilot explorando archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot puede tardar varios minutos en explorar, modificar archivos y ejecutar pruebas. Durante el proceso pueden aparecer los botones **Keep** y **Undo** en el editor. No tienes que seleccionarlos mientras el agente trabaja; cuando termine, tendrás la opción de conservar o deshacer cada cambio.

7. Cuando Copilot te lo indique, selecciona **Continue** para ejecutar las pruebas.

   ![Captura del cuadro de Copilot Chat que solicita permiso para ejecutar pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Puede que haya pausas y que algunas pruebas fallen durante el proceso. Copilot alterna entre generar código y ejecutar pruebas hasta completar la tarea y no detectar errores.

   ![Captura de una sesión completa de Copilot en modo de agente](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Inspecciona el código generado y busca posibles problemas.

> [!CAUTION]
> Es importante revisar el código generado por Copilot o cualquier otra herramienta de IA.

10. Vuelve al navegador con el sitio en ejecución y explora la funcionalidad nueva.
11. Cuando hayas confirmado que funciona y revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y siguientes pasos

Has explorado cómo utilizar el modo de agente de GitHub Copilot para añadir capacidades al sitio web de Tailspin Toys. En concreto, has aprendido cómo:

- el modo de agente de GitHub Copilot implementa funcionalidades en la interfaz y la capa de datos.
- el modo de agente explora el proyecto, localiza archivos pertinentes y coordina cambios.
- revisar los cambios y las pruebas generados antes de combinarlos en la base de código.

A continuación, [probarás la funcionalidad con el servidor MCP de Playwright][next-lesson] y abrirás una solicitud de incorporación de cambios.

### Ejercicio adicional: implementa la paginación

A medida que crezca la lista de juegos, será necesario habilitar la paginación. Utiliza las habilidades de este ejercicio y pide a Copilot que la implemente. Al definir el prompt:

- sigue las prácticas recomendadas y utiliza los archivos de instrucciones existentes.
- decide si los usuarios podrán seleccionar el tamaño de página o si será fijo.
- proporciona a Copilot las directrices necesarias para implementar el comportamiento que deseas.
- itera y aporta contexto adicional según sea necesario. Es el flujo normal al trabajar con Copilot.

## Recursos

- [Modos ask, edit y agent de Copilot: qué hacen y cuándo usarlos][choose-mode]
- [Modo de agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
