# Planar Studio 3.1.1

Aplicación CAD/CAM planar offline, contenida en un solo archivo HTML, para diseño de piezas planas, patrones paramétricos, uniones tipo slot/half-lap, organización para corte láser y análisis de ensamblaje.

## Corrección principal: Distribuir sobre pieza sigue el borde real

En 3.1.0, **Distribuir sobre pieza** colocaba las instancias sobre una recta definida por el eje U/V de la pieza guía. En una columna o espina curvada esto hacía que las costillas se separasen progresivamente de la pieza y terminasen flotando fuera de ella.

Planar Studio 3.1.1 sustituye ese comportamiento por una distribución guiada por el contorno:

1. Se obtiene el contorno Bézier adaptativo de la pieza guía.
2. Se determina su eje longitudinal U/V (manual o automático).
3. El contorno se divide en sus dos bordes longitudinales.
4. La posición de la pieza maestra decide, por defecto, cuál de esos bordes es el borde de trabajo.
5. Las estaciones se distribuyen por **longitud de arco**, no por una recta global.
6. El offset local de la pieza maestra respecto al borde se transporta con el marco local del contorno.
7. Con la orientación `Seguir tangente del borde`, cada instancia rota según la tangente local y permanece montada sobre la guía.

### Nuevos controles de Distribuir sobre pieza

En el diálogo contextual aparecen ahora:

- **Trayectoria**
  - `Borde donde está la maestra` — opción recomendada y predeterminada.
  - `Borde opuesto` — usa el otro borde longitudinal de la guía.
  - `Eje recto (compatibilidad)` — conserva el comportamiento lineal de 3.1.0 cuando sea útil.
- **Orientación**
  - `Seguir tangente del borde` — gira las costillas con la curvatura.
  - `Mantener orientación` — conserva la orientación 3D original mientras las posiciones siguen el borde.

La pieza maestra sigue siendo parte del patrón. Las instancias continúan vinculadas y participan en intersecciones, joints, ranuras, Ensamblaje, Corte 2D y nesting.

## Ejemplo columna + vértebras

1. Dibuja la columna o espina.
2. Coloca una sola vértebra/costilla correctamente sobre el borde deseado.
3. Clic derecho sobre esa pieza → **Distribuir sobre pieza…**.
4. Elige la columna como `Pieza guía`.
5. Deja `Trayectoria = Borde donde está la maestra`.
6. Deja `Orientación = Seguir tangente del borde`.
7. Define cantidad y márgenes.
8. Mantén activado **Actualizar uniones y ranuras al aplicar** si quieres regenerar automáticamente los joints.

La costilla original actúa como ancla geométrica. Las demás reproducen su relación local con el borde a lo largo de la curva.

## Patrones paramétricos disponibles

- **Simetría…** — U, V o plano de otra pieza.
- **Matriz rectangular…** — filas/columnas y separación U/V.
- **Matriz polar…** — copias alrededor del eje de otra pieza.
- **Distribuir sobre pieza…** — distribución sobre borde curvo o eje lineal.
- **Duplicar independiente**.

Las instancias pueden **Desvincularse** individualmente o el patrón completo puede **Fijarse** para convertir todas las instancias en piezas independientes.

## Compatibilidad

El formato de proyecto pasa a `version: 14`. Los patrones `along` anteriores sin el nuevo campo `pathMode` se migran al modo `edge-nearest`, que corrige automáticamente el comportamiento que hacía flotar las costillas en guías curvas. Sigue disponible `axis` para recuperar explícitamente el comportamiento lineal anterior.

## Uso

Abre `PlanarStudio.html` en un navegador moderno. No requiere servidor, instalación ni dependencias externas.

Las funciones anteriores siguen incluidas: editor Bézier 2D, planos 3D, gumball, Corte 2D, nesting con validación exacta, stocks, calibración, ranuras sensibles al ángulo, ensamblaje por desmontaje inverso, trayectorias multitramos, autosave, seguridad de importación y fallback CPU/WebGL.

Consulta `AUDIT.md` para las verificaciones de esta versión.
