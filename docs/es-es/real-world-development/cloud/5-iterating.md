---
title: "Ejercicio 5: Iterar sobre el trabajo de GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Ejercicio anterior: Gestión de agentes][previous-lesson] |
|:--|

## Revisa el trabajo

Durante este taller has trabajado con GitHub Copilot en varias tareas para mejorar la experiencia de usuario y añadir funcionalidades. Le has pedido que documente el código, cree una funcionalidad de juegos relacionados para que el equipo de diseño pueda evaluarla e implemente mejoras de accesibilidad con controles de alto contraste y modo claro. Ahora revisarás los cambios y, si es necesario, proporcionarás comentarios para mejorar el resultado.

### Escenario

Los principios del diseño de software y DevOps (desarrollo y operaciones) no cambian con la inteligencia artificial (IA) generativa. Hay que revisar el código generado y seguir el proceso DevOps habitual. Revisarás las propuestas de Copilot para la documentación, los juegos relacionados y la accesibilidad antes de habilitar la revisión del resto del equipo.

## Seguridad y agente de GitHub Copilot en la nube

Como el agente de Copilot en la nube realiza tareas asíncronas sin supervisión, se aplican varias restricciones de seguridad:

- Copilot solo tiene acceso de lectura al repositorio y acceso de escritura **únicamente** a la rama que utilizará para el código.
- El agente se ejecuta en GitHub Actions, en un entorno separado y efímero.
- Un usuario debe aprobar los flujos de trabajo de GitHub Actions antes de que se ejecuten.
- [El acceso a recursos externos está limitado de forma predeterminada](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall), incluidos los servidores MCP.

## Revisa la documentación generada

Empieza por la primera solicitud de incorporación de cambios (PR) creada por el agente de Copilot en la nube: la que añade documentación al código. Utilizarás la interfaz habitual de PR en GitHub.com.

> [!NOTE]
> Puede que veas un aviso que indica que el cortafuegos ha bloqueado a GitHub Copilot. Es lo esperado: el acceso del agente a recursos externos, incluidas las llamadas a servidores MCP externos, está limitado de forma predeterminada. Si quieres, puedes [personalizar o deshabilitar el cortafuegos del agente en la nube][agent-firewall].

1. Vuelve al repositorio en github.com.
2. Selecciona **Pull Requests** para abrir la lista de PR.
3. Abre la PR titulada **Add missing documentation** o con un título parecido.

> [!NOTE]
> Si Copilot aún está trabajando, la PR incluirá la etiqueta **[WIP]**. Espera a que termine; puede tardar unos minutos.

4. Cuando la PR esté lista, selecciona la pestaña **Files changed** y revisa los cambios.

   ![Captura de la pestaña Files changed](../../../_images/shared-pr-files-changed.png)

5. Examina el código actualizado. Los cambios exactos pueden variar. Comprueba que haya comentarios TSDoc, que se respeten las convenciones de TypeScript y que los archivos incluyan encabezados de comentario. Estas directrices proceden de los archivos de instrucciones que revisaste al inicio del ejercicio 2.
6. Cuando hayas terminado la revisión, vuelve a la pestaña **Conversation** y desplázate hacia abajo.
7. Comprueba si hay flujos de trabajo pendientes de aprobación.

8. Si los hay, selecciona **Approve and run workflows**.

   ![Captura de la opción Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. Comprueba que los flujos aparecen en la sección de comprobaciones de la PR. Si todo va bien, las comprobaciones del proyecto se completarán correctamente para la aplicación Astro. Puede tardar unos minutos.

## Solicita cambios a GitHub Copilot

Puedes mencionar a Copilot en un comentario de la PR o en un comentario insertado en el código para pedirle cambios. Copilot leerá el comentario e iniciará otra sesión para atenderlo. Como los resultados son no deterministas, no hay un prompt único que debas utilizar. Puedes pedirle que:

- añada a cada archivo de código un encabezado de comentario breve que describa su propósito.
- añada comentarios TSDoc a los archivos TypeScript y Astro.
- cree un README que describa la estructura de la aplicación Astro.

1. Añade un comentario en el que menciones a **@copilot** y solicites un cambio en la documentación generada. Utiliza una de las ideas anteriores u otra mejora que quieras ver en la base de código.
2. Selecciona **View Session** para ver el trabajo de Copilot. Iniciará una sesión nueva para realizar los cambios.
3. Si quieres volver a la PR, selecciona **Back to pull request**.

   ![Captura de la opción Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Cuando Copilot termine los cambios, comprueba que se haya añadido un commit a la PR.
5. Selecciona **Files changed** y revisa los cambios. Puedes seguir iterando hasta que estés conforme; después, puedes marcar la PR como lista para revisión y combinarla en la rama principal.

   ![Captura de la opción Convert PR to ready](../../../_images/ex7-ready-for-review.png)

## Revisa la funcionalidad de juegos relacionados

Ahora revisarás la PR que Copilot creó para mostrar juegos relacionados en la página de detalles de cada juego.

1. Vuelve al repositorio en GitHub.com.
2. Selecciona la pestaña **Pull Requests**.
3. Selecciona la PR titulada **Show related games on the game details page** o con un título parecido.
4. Selecciona **Files changed** y revisa el código generado.
5. Cuando termines, vuelve a **Conversation** y desplázate hacia abajo.
6. Comprueba si hay flujos de trabajo pendientes de aprobación.

7. Si los hay, selecciona **Approve and run workflows**.

   ![Captura de la opción Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. Comprueba que los flujos aparecen en las comprobaciones de la PR y espera a que se completen correctamente.
9. **Opcional:** prueba manualmente la funcionalidad de juegos relacionados desde tu Codespace. Abre el terminal y ejecuta los comandos siguientes; sustituye `<branch-name>` por el nombre de la rama creada por Copilot, por ejemplo, `copilot/fix-8`:

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot ha creado la funcionalidad de juegos relacionados. Puedes seguir iterando con el agente en la nube y pedir cambios, como ajustar el número de juegos relacionados o añadir encabezados de comentario y comentarios TSDoc. Recuerda que esta tarea se asignó **antes** de actualizar las instrucciones personalizadas. Añade la solicitud como comentario en la pestaña **Conversation**; Copilot la leerá e iniciará otra sesión.

## Revisa las funciones de accesibilidad

Por último, revisarás la PR creada por el agente personalizado de accesibilidad. Debe incluir el modo de alto contraste que asignaste en el ejercicio 3 y el modo claro que solicitaste desde Mission Control en el ejercicio 4.

1. Vuelve al repositorio en GitHub.com.
2. Selecciona la pestaña **Pull Requests**.
3. Selecciona la PR titulada **Add high contrast mode to website** o con un título parecido.

> [!NOTE]
> Si Copilot todavía está trabajando, la PR tendrá la etiqueta **[WIP]**. Espera a que termine.

4. Selecciona **Files changed** para revisar el código.
5. Presta atención a:
   - los controles de interfaz para cambiar de modo.
   - el uso de local storage para guardar las preferencias.
   - los cambios de hojas de estilo en cascada (CSS) para los modos de alto contraste y claro.
   - los atributos de accesibilidad, como las etiquetas de aplicaciones de Internet enriquecidas y accesibles (ARIA) y la navegación con teclado.
   - el código JavaScript o TypeScript que gestiona el cambio de modo.

6. Cuando termines, vuelve a la pestaña **Conversation** y desplázate hacia abajo.
7. Comprueba si hay flujos de trabajo pendientes de aprobación.

8. Si los hay, selecciona **Approve and run workflows**.

   ![Captura de la opción Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. Espera a que terminen los flujos y comprueba que se completen correctamente.
10. **Opcional:** cambia a la rama del agente en tu Codespace para probar manualmente las funciones de accesibilidad. Abre un terminal y ejecuta estos comandos; sustituye `<branch-name>` por el nombre de la rama creada por Copilot:

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

   Después, inicia la aplicación y prueba los controles de alto contraste y modo claro en el navegador. Comprueba que funcionan y que las preferencias se conservan al volver a cargar la página.

El agente personalizado de accesibilidad ha orientado a Copilot para que implemente estas funciones siguiendo las prácticas recomendadas de accesibilidad. Si encuentras algún problema o mejora posible, puedes mencionar a **@copilot** en un comentario y pedir una actualización.

## Ejercicio opcional: sigue delegando

El agente en la nube resulta útil para asignarle elementos reales del backlog y ocuparse de otra tarea mientras trabaja. Para practicar, crea más incidencias en tu repositorio y asígnaselas a Copilot. Por ejemplo:

- Crear un formulario de interés para patrocinadores en la página de detalles del juego.
- Implementar la paginación de la lista de juegos.
- Añadir validación de entradas y gestión de errores a los helpers de acceso a datos.

## Resumen

Has completado el recorrido del agente en la nube. A lo largo de estos ejercicios:

- **Has inspeccionado los archivos de instrucciones personalizados del repositorio** para ver su efecto en el resultado del agente.
- **Has asignado incidencias al agente de Copilot en la nube** y has observado cómo configura el entorno, planifica y trabaja de forma asíncrona.
- **Has creado y utilizado un agente personalizado** para implementar los modos de alto contraste y claro.
- **Has utilizado la página de agentes de Copilot como centro de control** para supervisar y orientar la sesión de accesibilidad.
- **Has revisado e iterado sobre las PR del agente en la nube**, mencionando a `@copilot` para solicitar cambios y aprobando flujos de trabajo.

## Repaso y siguientes pasos

Has completado el recorrido del agente en la nube. Si quieres seguir explorando, los otros recorridos complementan lo que has practicado:

- **[Recorrido de VS Code](../../vscode/)**: explora el modo de agente de Copilot Chat y la integración de MCP desde el IDE.
- **[Recorrido de CLI](../../cli/)**: entrega dos cambios revisados desde el terminal con Plan y Autopilot, habilidades de agente, un agente QA personalizado, Playwright MCP, Agent Merge y el comando opcional `/delegate`.

En tu propio repositorio también puedes probar estas ideas:

- Asignar al agente en la nube una incidencia de refactorización o cobertura de pruebas.
- Crear un agente personalizado para otro ámbito.
- Configurar `copilot-setup-steps.yml` para otra pila tecnológica.

## Recursos

- [GitHub Copilot][github-copilot]
- [Acerca de los agentes de Copilot][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Buenas prácticas para el flujo de configuración del agente en la nube][cloud-agent-best-practices]
- [Configurar el cortafuegos del agente en la nube][agent-firewall]

---

| [← Ejercicio anterior: Gestión de agentes][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
