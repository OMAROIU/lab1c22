##flor marina torres jandres 
##Omar salvador garcia vasquez 


### 1. ¿Qué es Vue.js y cuál es su función dentro de la página web desarrollada?

Vue.js es un framework progresivo de JavaScript que permite construir interfaces de usuario de forma **reactiva y declarativa**. Su principal característica es que vincula el estado de los datos directamente con lo que se muestra en pantalla: cuando una variable cambia, la interfaz se actualiza automáticamente sin necesidad de manipular el DOM de forma manual.

En **EduRegistro**, Vue.js cumple las siguientes funciones:

- **Reactividad**: Cuando se agrega, edita o elimina un estudiante, las tarjetas de resumen (promedio, aprobados, reprobados) se recalculan y actualizan de forma inmediata.
- **Componentización**: La aplicación está dividida en 5 componentes reutilizables (`AppHeader`, `ToastMsg`, `ResumenCards`, `FormularioEstudiante`, `TablaEstudiantes`), cada uno con su propia lógica y estilos.
- **Enlace de datos**: Todos los campos del formulario están sincronizados con variables reactivas mediante `v-model`.
- **Renderizado condicional y dinámico**: Controla qué secciones se muestran con `v-if` y genera listas automáticamente con `v-for`.

---

### 2. Variables reactivas utilizadas y su función

Todas las variables reactivas están declaradas en `App.vue` con `ref()` o `reactive()` de la Composition API de Vue 3.

| Variable | Declaración | Función dentro del sistema |
|---|---|---|
| `estudiantes` | `ref([])` | Lista principal con todos los registros de estudiantes. Es la fuente de verdad de la aplicación. |
| `busqueda` | `ref('')` | Almacena el texto del buscador. Cuando cambia, la lista filtrada se recalcula automáticamente. |
| `filtroActivo` | `ref('todos')` | Controla qué pestaña de filtro está activa: `'todos'`, `'aprobado'`, `'advertencia'` o `'reprobado'`. |
| `modoEdicion` | `ref(false)` | Indica si el formulario está en modo edición (`true`) o nuevo registro (`false`). Cambia el texto del botón y el título del panel. |
| `editId` | `ref(null)` | Guarda el ID del estudiante que se está editando para localizarlo dentro del array y actualizarlo. |
| `form` | `reactive({})` | Objeto reactivo que representa todos los campos del formulario: carnet, nombre, materia, sección, nota y observación. |
| `errores` | `reactive({})` | Objeto reactivo con los mensajes de error de validación por campo. Se muestra debajo de cada input. |
| `toast` | `reactive({})` | Controla la notificación emergente: su visibilidad, texto y tipo (éxito, advertencia, error). |

---

### 3. Diferencia entre `v-bind` y `v-model`

**`v-bind`** establece un enlace **unidireccional** de Vue hacia el DOM. Vincula dinámicamente el valor de un atributo HTML a una expresión de Vue. El DOM refleja los cambios de la variable, pero los cambios en el elemento no actualizan la variable.

**`v-model`** establece un enlace **bidireccional** entre un campo de formulario y una variable reactiva. Cuando el usuario escribe o selecciona algo, la variable se actualiza; y cuando la variable cambia desde el código, el campo también se actualiza visualmente.

**Ejemplos usados en este proyecto:**

```html
<!-- v-bind en FormularioEstudiante.vue:
     Aplica la clase 'input-err' dinámicamente si hay error de validación -->
<input
  v-model="form.carnet"
  :class="{ 'input-err': errores.carnet }"
  type="text"
/>

<!-- v-bind en TablaEstudiantes.vue:
     Colorea el badge de nota según el resultado de claseNota() -->
<span :class="['badge', claseNota(Number(est.nota))]">
  {{ Number(est.nota).toFixed(1) }}
</span>

<!-- v-bind en ResumenCards.vue:
     Cambia el color del promedio general según si es aprobado o reprobado -->
<span :class="['val', claseNota(promedio)]">{{ promedio.toFixed(1) }}</span>

<!-- v-model en FormularioEstudiante.vue:
     Sincroniza el input de texto con la variable form.nombre -->
<input v-model="form.nombre" type="text" placeholder="Apellido, Nombre" />

<!-- v-model en FormularioEstudiante.vue:
     El slider y el input numérico comparten el mismo v-model (form.nota),
     por lo que al mover el slider el número se actualiza y viceversa -->
<input type="range"  v-model="form.nota" min="0" max="10" step="0.1" />
<input type="number" v-model="form.nota" min="0" max="10" step="0.1" />
```

En resumen: `v-bind` es de **una sola vía** (Vue → DOM) y `v-model` es de **doble vía** (Vue ↔ DOM).

---

### 4. Ejemplo de evento utilizado

En la aplicación se usan varios eventos. El más importante es `@click` en el botón principal del formulario en `FormularioEstudiante.vue`:

```html
<!-- @click: emite el evento 'guardar' hacia App.vue al hacer clic -->
<button class="btn btn-primary" @click="$emit('guardar')">
  {{ modoEdicion ? 'Actualizar' : 'Agregar' }}
</button>
```

App.vue recibe este evento y ejecuta la función `guardar()`, que valida los datos y agrega o actualiza el registro.

Otros eventos usados en el proyecto:

```html
<!-- @input: limpia el mensaje de error en tiempo real mientras el usuario escribe -->
<input v-model="form.carnet" @input="errores.carnet = ''" />

<!-- @change: limpia el error de materia cuando el usuario cambia la selección -->
<select v-model="form.materia" @change="errores.materia = ''">

<!-- @click en TablaEstudiantes.vue: emite el evento eliminar con el id del registro -->
<button class="act red" @click="$emit('eliminar', est.id)">✕</button>

<!-- @click en TablaEstudiantes.vue: emite el evento editar con el objeto estudiante -->
<button class="act" @click="$emit('editar', est)">✎</button>

<!-- @click en TablaEstudiantes.vue: emite el filtro seleccionado hacia App.vue -->
<button @click="$emit('filtrar', f.valor)">{{ f.label }}</button>
```

---

### 5. ¿Para qué se utilizó `v-for`?

La directiva `v-for` se usó para generar listas de elementos HTML de forma dinámica, sin escribir código repetitivo. Se aplicó en cuatro lugares del proyecto:

```html
<!-- 1. En TablaEstudiantes.vue: genera una fila por cada estudiante filtrado -->
<tr v-for="est in estudiantes" :key="est.id">

<!-- 2. En TablaEstudiantes.vue: genera los botones de filtro dinámicamente -->
<button
  v-for="f in filtros"
  :key="f.valor"
  @click="$emit('filtrar', f.valor)"
>{{ f.label }}</button>

<!-- 3. En FormularioEstudiante.vue: genera las opciones del select de materias -->
<option v-for="m in materias" :key="m" :value="m">{{ m }}</option>

<!-- 4. En FormularioEstudiante.vue: genera las opciones del select de secciones -->
<option v-for="s in secciones" :key="s" :value="s">{{ s }}</option>
```

Gracias a `v-for`, cuando se agrega o elimina un estudiante, la tabla se actualiza automáticamente porque Vue re-renderiza solo las filas que cambiaron, identificándolas mediante el atributo `:key`.

---

### 6. ¿En qué situación se utilizó `v-if` y qué problema resuelve?

`v-if` se usó para mostrar u ocultar elementos de la interfaz según condiciones específicas, evitando mostrar información innecesaria o confusa al usuario:

```html
<!-- 1. En App.vue: muestra el resumen solo cuando hay estudiantes registrados.
        Sin esto, las tarjetas aparecerían con todos los valores en 0
        al entrar por primera vez. -->
<ResumenCards v-if="estudiantes.length > 0" ... />

<!-- 2. En App.vue: muestra la tabla solo si hay registros.
        Evita renderizar una tabla vacía sin sentido. -->
<TablaEstudiantes v-if="estudiantes.length > 0" ... />

<!-- 3. En ToastMsg.vue: muestra la notificación solo cuando hay un mensaje activo.
        De lo contrario permanece invisible sin ocupar espacio. -->
<div v-if="toast.visible" :class="['toast', toast.tipo]">

<!-- 4. En TablaEstudiantes.vue: muestra la fila "Sin resultados" solo cuando
        la búsqueda o el filtro no devuelven ningún estudiante. -->
<tr v-if="estudiantes.length === 0">
  <td colspan="7" class="empty">Sin resultados</td>
</tr>

<!-- 5. En FormularioEstudiante.vue: muestra el botón "Cancelar" únicamente
        cuando se está editando un registro existente. En modo de nuevo
        registro este botón no tiene utilidad. -->
<button class="btn btn-ghost" v-if="modoEdicion" @click="$emit('limpiar')">
  Cancelar
</button>
```

---

### 7. ¿Cómo se realiza la validación de datos y por qué es importante?

**¿Cómo se realiza?**

La validación está centralizada en la función `validar()` dentro de `App.vue`. Se ejecuta cada vez que el usuario presiona el botón Agregar o Actualizar, verificando cada campo antes de permitir el guardado:

```javascript
function validar() {
  let ok = true
  const n = Number(form.nota)

  // Carnet: obligatorio, formato válido y sin duplicados
  if (!form.carnet.trim()) {
    errores.carnet = 'El carnet es obligatorio.'; ok = false
  } else if (!/^[A-Za-z0-9\-]+$/.test(form.carnet.trim())) {
    errores.carnet = 'Solo letras, números y guiones.'; ok = false
  } else if (!modoEdicion.value && estudiantes.value.some(
    e => e.carnet.toLowerCase() === form.carnet.trim().toLowerCase()
  )) {
    errores.carnet = 'Carnet ya registrado.'; ok = false
  }

  // Nombre: obligatorio y mínimo 5 caracteres
  if (!form.nombre.trim()) {
    errores.nombre = 'El nombre es obligatorio.'; ok = false
  } else if (form.nombre.trim().length < 5) {
    errores.nombre = 'Nombre demasiado corto.'; ok = false
  }

  // Materia: obligatoria
  if (!form.materia) {
    errores.materia = 'Selecciona una materia.'; ok = false
  }

  // Nota: número entre 0 y 10
  if (isNaN(n) || n < 0 || n > 10) {
    errores.nota = 'La nota debe estar entre 0 y 10.'; ok = false
  }

  return ok
}
```

Los mensajes de error se almacenan en el objeto reactivo `errores` y se muestran debajo de cada campo en `FormularioEstudiante.vue`. El evento `@input` en cada campo los limpia automáticamente mientras el usuario corrige la información.

**¿Por qué es importante validar?**

- **Integridad de los datos**: Un carnet duplicado o un nombre vacío generaría registros inutilizables en el sistema.
- **Cálculos correctos**: Una nota fuera del rango (por ejemplo, 15 o -2) haría que el promedio general y el conteo de aprobados fueran incorrectos.
- **Experiencia del usuario**: Los mensajes de error específicos por campo guían al usuario sobre qué debe corregir, en lugar de rechazar el formulario sin explicación.
- **Prevención de datos basura**: Mantiene el registro limpio y confiable, lo cual es crítico en un sistema académico donde los datos impactan decisiones reales sobre los estudiantes.



