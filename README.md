# OpoTest 2.0 · Ingeniería de Topografía

PWA para Android, sin dependencias de ejecución: HTML, CSS y JavaScript. Incluye aprendizaje, examen, banco de preguntas, estadísticas y funcionamiento sin conexión mediante Service Worker.

## Actualizar desde la versión 1

1. En OpoTest 1, entra en Ajustes > Exportar copia completa y guarda el JSON fuera de la carpeta del proyecto. Termina cualquier test en curso antes de actualizar. Las copias restauran banco, progreso, preferencias e historial, pero no el test abierto.
2. Detén el servidor de PyCharm con Ctrl+C. Descomprime la actualización y copia su contenido **dentro de la carpeta `opotest` existente**, aceptando sustituir los archivos coincidentes. No crees una carpeta opotest dentro de otra. La actualización modifica app.mjs, core.mjs, styles.css, sw.js, README.md, datos/plantilla_preguntas.csv y tests/core.test.mjs. Conserva tus otros archivos.
3. En la terminal de PyCharm, desde la carpeta donde está index.html, ejecuta `python -m http.server 8000` (o `py -m http.server 8000`). Abre http://localhost:8000 exactamente como antes. Ctrl+F5 y, si fuera necesario, cierra y vuelve a abrir la pestaña. En Ajustes debe aparecer versión 2.0.
4. La clave del almacenamiento sigue siendo `opotest.v1`; se mantienen tus preguntas y progreso en el mismo navegador, perfil, protocolo, host y puerto. Si cambias de URL (p. ej. GitHub Pages), exporta/restaura la copia; no se transfiere automáticamente. No borres el almacenamiento del sitio para forzar la actualización.

## Nuevas funciones

- **Simulacro de examen** desde Inicio: elige 10, 20, 30, 50, 75 o 100 preguntas, tiempo de 0 (sin límite) a 180 minutos, penalización por fallo y temas/subtemas. Por defecto, selección aleatoria y 30 minutos sin penalización; no son reglas oficiales de la oposición. Puedes cambiar respuestas, retroceder, saltar a otra pregunta y dejar respuestas en blanco. No muestra aciertos ni explicaciones hasta entregar. El reloj continúa al cerrar la app o bloquear el móvil; al volver, si venció el plazo se corrige automáticamente. Mientras la app está abierta comprueba el plazo cada 500 ms y también antes de procesar los botones. No se registra una respuesta del examen en el progreso hasta entregar o expirar; las preguntas en blanco no cuentan como falladas para el algoritmo. Si descartas el examen se pierden sus respuestas no entregadas.
- **Puntuación:** nota sobre 10 = 10 × (aciertos − fallos × penalización) / preguntas efectivamente incluidas. Los blancos valen 0. Puede ser negativa. El modo aprendizaje conserva corrección inmediata y no penaliza.
- **Test personalizado:** combina tantos temas y subtemas como quieras y elige selección aleatoria o adaptativa. No hay preguntas repetidas dentro del test; si hay menos que las solicitadas, se usan las disponibles. La selección se realiza sobre la unión de los bloques, no reparte por igual entre ellos.
- **Imágenes:** campo opcional `imagen` y texto alternativo `imagen_alt`; el CSV/JSON anterior sigue siendo compatible. Formatos PNG, JPEG, WebP, GIF. Para uso privado y offline, incorpora una imagen pequeña en el propio JSON como `data:image/png;base64,...` (máximo 280 000 caracteres por imagen; aproximadamente 205 KiB de archivo). Alternativamente puedes usar `assets/preguntas/esquema_001.png`: coloca el archivo en esa ruta y publícalo con la app; solo estará disponible offline si se ha cargado previamente con el Service Worker activo. No publiques imágenes privadas en repositorios públicos. Las copias incluyen imágenes embebidas, no los archivos externos. LocalStorage tiene capacidad limitada (normalmente alrededor de 5 MiB por origen, variable según navegador); para bancos con muchas imágenes será necesario migrar a IndexedDB. La importación y restauración revierten si falla el guardado. No se admiten URLs externas, SVG ni PDF. El CSV nuevo tiene dos columnas opcionales al final, sin necesidad de modificar bancos previos.
- **Progreso:** gráfica de notas sobre 10 de los últimos 30 tests completados, también los antiguos. Combina aprendizaje y examen, así que las notas con distinta penalización o dificultad no son estrictamente comparables.
- **Ajustes > Algoritmo adaptativo:** distribuye porcentajes entre falladas, nuevas, por consolidar/repasar y consolidadas; la suma debe ser 100. Por defecto 40/30/20/10. La aplicación redondea las cuotas y rellena con otras categorías si no hay suficientes. El repaso espaciado sigue siendo 1/3/7/14/30 días y el umbral de consolidación, 3 aciertos seguidos.

## Banco de preguntas

El CSV usa encabezados `id;tema;subtema;pregunta;opcion_a;opcion_b;opcion_c;opcion_d;correcta;explicacion;dificultad;fuente;apartado;pagina;imagen;imagen_alt`. JSON: lista de objetos con los mismos campos. Se requiere ID único, tema, pregunta, cuatro opciones distintas, correcta A/B/C/D y explicación. Importa desde Ajustes. Si cambia el enunciado, las opciones, la correcta o la imagen de un ID existente, se reinicia el progreso individual. Los 480 registros del convenio son compatibles sin modificación; no se incluyen en el código publicado.

## Publicación en GitHub Pages (primera instalación)

1. Crea un repositorio público (por ejemplo `opotest`) en GitHub, rama main. La web será pública; no subas bancos privados ni copias de seguridad.
2. Sube el **contenido** de la carpeta opotest a la raíz del repositorio: index.html, app.mjs, core.mjs, styles.css, sw.js, manifest.webmanifest, assets/ y datos/ (solo el demo y la plantilla). No necesitas subir tests/ ni las automatizaciones de .github.
3. En Settings > Pages > Build and deployment, elige Deploy from a branch, main, /(root), Save. Espera a que GitHub indique que la web está publicada. Abre la URL HTTPS que GitHub muestra, normalmente `https://TU_USUARIO.github.io/opotest/`. Si usas la publicación desde rama, no configures a la vez GitHub Actions.
4. En Chrome de Android abre esa URL. Menú ⋮ > Instalar aplicación (o Añadir a pantalla de inicio, según versión). Abre OpoTest desde el icono. Importa el JSON del convenio o restaura una copia del ordenador en Ajustes. No necesitas encender el ordenador para usarla.
5. Tras la primera visita online, prueba el modo avión: cierra/abre la aplicación y realiza un test. Exporta copias periódicamente; borrar datos de Chrome, cambiar de móvil o de URL puede hacer perder el progreso local.

## Pruebas recomendadas antes de instalar

- Verifica que en Ajustes aparece 2.0 y que están las preguntas y el progreso de v1.
- Inicia un examen de 10 preguntas y 1 minuto; selecciona dos subtemas, penalización 1/3, marca una respuesta, retrocede, déjala en blanco y comprueba que no muestra la solución. Deja vencer el tiempo. Revisa la nota y los blancos.
- Realiza un test personalizado y un test de aprendizaje; comprueba las estadísticas y modifica los pesos en Ajustes.
- Exporta una copia. Instala en Android y repite la prueba de persistencia y modo avión.

## Pruebas de código (opcional)

Si tienes Node.js, desde la carpeta del proyecto ejecuta `node --test tests/core.test.mjs`. Las 15 pruebas del motor pasan en el entorno de desarrollo, así como la comprobación sintáctica y la importación real de las 480 preguntas. La comprobación visual en Chromium fue bloqueada por la política del entorno (ERR_BLOCKED_BY_ADMINISTRATOR); el funcionamiento en navegador real, la instalación Android y el modo offline deben verificarse localmente. El código no incluye telemetría, sincronización en nube ni generación automática de preguntas.
