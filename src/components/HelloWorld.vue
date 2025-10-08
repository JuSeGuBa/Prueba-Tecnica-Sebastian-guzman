<template>
  <div
    class="min-h-screen bg-gradient-to-br from-gray-800 to-blue-900 py-8 px-4"
  >
    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="text-center mb-12">
        <h1 class="text-4xl font-bold text-white mb-3">Tus Tareas</h1>
        <p class="text-lg text-gray-300">
          Gestiona tus tareas para cumplir con tu objetivo
        </p>
      </div>

      <!-- Stats Cards -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
        <div class="bg-white rounded-2xl shadow-lg p-6 text-center">
          <div class="text-3xl font-bold text-blue-600 mb-2">
            {{ totalTasks }}
          </div>
          <div class="text-gray-600 font-medium">Total Tareas</div>
        </div>
        <div class="bg-white rounded-2xl shadow-lg p-6 text-center">
          <div class="text-3xl font-bold text-green-600 mb-2">
            {{ completedTasks }}
          </div>
          <div class="text-gray-600 font-medium">Completadas</div>
        </div>
        <div class="bg-white rounded-2xl shadow-lg p-6 text-center">
          <div class="text-3xl font-bold text-orange-600 mb-2">
            {{ pendingTasks }}
          </div>
          <div class="text-gray-600 font-medium">Pendientes</div>
        </div>
      </div>

      <!-- Main Content -->
      <div class="bg-white rounded-2xl shadow-xl overflow-hidden">
        <!-- Create Task Card -->
        <div class="p-8 border-b border-gray-200">
          <h2 class="text-2xl font-semibold text-gray-800 mb-6">
            Crear Nueva Tarea
          </h2>

          <form @submit.prevent="createTask" class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2"
                >Título de la tarea</label
              >
              <input
                v-model="newTitle"
                type="text"
                class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                placeholder="¿Qué necesitas hacer?"
                required
              />
            </div>

            <!-- Technologies Section -->
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-3"
                >Tecnologías & Skills</label
              >

              <!-- Selected Keywords -->
              <div
                v-if="selectedKeywords.length > 0"
                class="flex flex-wrap gap-2 mb-4 p-3 bg-gray-50 rounded-lg"
              >
                <span
                  v-for="keywordId in selectedKeywords"
                  :key="keywordId"
                  class="inline-flex items-center px-3 py-2 rounded-full text-sm font-medium bg-gradient-to-r from-blue-500 to-indigo-600 text-white shadow-sm"
                >
                  {{ getKeywordName(keywordId) }}
                  <button
                    type="button"
                    @click="removeKeyword(keywordId)"
                    class="ml-2 hover:text-blue-200 focus:outline-none font-bold text-white"
                  >
                    ×
                  </button>
                </span>
              </div>

              <!-- Keyword Selector -->
              <div class="relative mb-4">
                <select
                  v-model="tempKeyword"
                  @change="addKeyword"
                  class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent appearance-none bg-white"
                >
                  <option value="">Agregar tecnología...</option>
                  <option
                    v-for="k in availableKeywords"
                    :key="k.id"
                    :value="k.id"
                  >
                    {{ k.name }}
                  </option>
                </select>
                <div
                  class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
                >
                  <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
                    <path
                      fill-rule="evenodd"
                      d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
                      clip-rule="evenodd"
                    />
                  </svg>
                </div>
              </div>

              <!-- Popular Technologies -->
              <div class="mb-4">
                <p class="text-sm text-gray-600 mb-3">Tecnologías populares:</p>
                <div
                  class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2"
                >
                  <button
                    v-for="k in popularKeywords"
                    :key="k.id"
                    type="button"
                    @click="toggleKeyword(k.id)"
                    :class="[
                      'px-3 py-2 rounded-lg text-sm font-medium transition-all duration-200 transform hover:scale-105 border-2 flex items-center justify-center',
                      selectedKeywords.includes(k.id)
                        ? 'bg-green-500 text-white border-green-500 shadow-md'
                        : 'bg-white text-gray-700 border-gray-300 hover:border-green-400 hover:bg-green-50',
                    ]"
                  >
                    <span v-if="selectedKeywords.includes(k.id)" class="mr-1"
                      >✓</span
                    >
                    {{ k.name }}
                  </button>
                </div>
              </div>

              <!-- Technology Categories -->
              <div class="space-y-4">
                <!-- Frontend -->
                <div>
                  <p class="text-sm font-medium text-gray-700 mb-2">Frontend</p>
                  <div class="flex flex-wrap gap-2">
                    <button
                      v-for="k in frontendKeywords"
                      :key="k.id"
                      type="button"
                      @click="toggleKeyword(k.id)"
                      :class="[
                        'px-3 py-1 rounded-full text-xs font-medium transition-all duration-200',
                        selectedKeywords.includes(k.id)
                          ? 'bg-blue-500 text-white shadow-sm'
                          : 'bg-blue-100 text-blue-700 hover:bg-blue-200',
                      ]"
                    >
                      {{ k.name }}
                    </button>
                  </div>
                </div>

                <!-- Backend -->
                <div>
                  <p class="text-sm font-medium text-gray-700 mb-2">Backend</p>
                  <div class="flex flex-wrap gap-2">
                    <button
                      v-for="k in backendKeywords"
                      :key="k.id"
                      type="button"
                      @click="toggleKeyword(k.id)"
                      :class="[
                        'px-3 py-1 rounded-full text-xs font-medium transition-all duration-200',
                        selectedKeywords.includes(k.id)
                          ? 'bg-green-500 text-white shadow-sm'
                          : 'bg-green-100 text-green-700 hover:bg-green-200',
                      ]"
                    >
                      {{ k.name }}
                    </button>
                  </div>
                </div>

                <!-- Database & Tools -->
                <div>
                  <p class="text-sm font-medium text-gray-700 mb-2">
                    Database & Tools
                  </p>
                  <div class="flex flex-wrap gap-2">
                    <button
                      v-for="k in databaseKeywords"
                      :key="k.id"
                      type="button"
                      @click="toggleKeyword(k.id)"
                      :class="[
                        'px-3 py-1 rounded-full text-xs font-medium transition-all duration-200',
                        selectedKeywords.includes(k.id)
                          ? 'bg-purple-500 text-white shadow-sm'
                          : 'bg-purple-100 text-purple-700 hover:bg-purple-200',
                      ]"
                    >
                      {{ k.name }}
                    </button>
                  </div>
                </div>
              </div>

              <div
                v-if="keywords.length === 0"
                class="text-sm text-orange-600 bg-orange-50 p-3 rounded-lg mt-4"
              >
                💡 <strong>Tip:</strong> Primero crea algunas tecnologías usando
                el API.
              </div>
            </div>

            <div class="flex items-center justify-between pt-4">
              <div
                v-if="error"
                class="text-red-600 text-sm font-medium flex items-center"
              >
                <svg
                  class="w-4 h-4 mr-1"
                  fill="currentColor"
                  viewBox="0 0 20 20"
                >
                  <path
                    fill-rule="evenodd"
                    d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z"
                    clip-rule="evenodd"
                  />
                </svg>
                {{ error }}
              </div>
              <div class="flex-1"></div>
              <button
                :disabled="loading"
                :class="[
                  'px-8 py-3 rounded-xl font-semibold text-white transition-all duration-200 flex items-center',
                  loading
                    ? 'bg-gray-400 cursor-not-allowed'
                    : 'bg-gradient-to-r from-blue-500 to-indigo-600 hover:from-blue-600 hover:to-indigo-700 shadow-lg hover:shadow-xl transform hover:-translate-y-0.5',
                ]"
              >
                <svg
                  v-if="loading"
                  class="animate-spin -ml-1 mr-3 h-5 w-5 text-white"
                  fill="none"
                  viewBox="0 0 24 24"
                >
                  <circle
                    class="opacity-25"
                    cx="12"
                    cy="12"
                    r="10"
                    stroke="currentColor"
                    stroke-width="4"
                  ></circle>
                  <path
                    class="opacity-75"
                    fill="currentColor"
                    d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                  ></path>
                </svg>
                {{ loading ? "Creando..." : "Crear Tarea" }}
              </button>
            </div>
          </form>
        </div>

        <!-- Tasks List -->
        <div class="p-8">
          <div class="flex items-center justify-between mb-6">
            <h2 class="text-2xl font-semibold text-gray-800">Mis Tareas</h2>
            <div class="flex items-center space-x-4">
              <button
                @click="fetchTasks"
                class="px-4 py-2 text-sm font-medium text-gray-700 bg-gray-100 rounded-lg hover:bg-gray-200 transition-colors duration-200 flex items-center"
              >
                <svg
                  class="w-4 h-4 mr-2"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                  />
                </svg>
                Actualizar
              </button>
            </div>
          </div>

          <!-- Empty State -->
          <div
            v-if="tasks.length === 0"
            class="text-center py-12 px-4 rounded-2xl bg-gray-50 border-2 border-dashed border-gray-300"
          >
            <svg
              class="w-16 h-16 text-gray-400 mx-auto mb-4"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"
              />
            </svg>
            <h3 class="text-lg font-medium text-gray-900 mb-2">
              No hay tareas
            </h3>
            <p class="text-gray-500">
              Comienza creando tu primera tarea arriba.
            </p>
          </div>

          <!-- Tasks Grid -->
          <div v-else class="grid gap-4">
            <div
              v-for="task in tasks"
              :key="task.id"
              :class="[
                'relative rounded-xl p-6 border-2 transition-all duration-300 transform hover:scale-[1.02]',
                task.is_done
                  ? 'bg-green-50 border-green-200 shadow-sm'
                  : 'bg-white border-gray-200 shadow-lg hover:shadow-xl',
              ]"
            >
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <div class="flex items-start space-x-3">
                    <button
                      @click="toggle(task.id)"
                      :class="[
                        'mt-1 w-6 h-6 rounded-full border-2 flex-shrink-0 transition-all duration-200 transform hover:scale-110',
                        task.is_done
                          ? 'bg-green-500 border-green-500 text-white'
                          : 'border-gray-300 hover:border-green-400',
                      ]"
                    >
                      <svg
                        v-if="task.is_done"
                        class="w-4 h-4 mx-auto"
                        fill="currentColor"
                        viewBox="0 0 20 20"
                      >
                        <path
                          fill-rule="evenodd"
                          d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                          clip-rule="evenodd"
                        />
                      </svg>
                    </button>

                    <div class="flex-1">
                      <h3
                        :class="[
                          'text-lg font-semibold transition-all duration-200',
                          task.is_done
                            ? 'text-green-700 line-through'
                            : 'text-gray-800',
                        ]"
                      >
                        {{ task.title }}
                      </h3>

                      <div class="flex items-center mt-2 space-x-4">
                        <span
                          :class="[
                            'inline-flex items-center px-3 py-1 rounded-full text-sm font-medium',
                            task.is_done
                              ? 'bg-green-100 text-green-800'
                              : 'bg-orange-100 text-orange-800',
                          ]"
                        >
                          <span
                            :class="[
                              'w-2 h-2 rounded-full mr-2',
                              task.is_done ? 'bg-green-500' : 'bg-orange-500',
                            ]"
                          ></span>
                          {{ task.is_done ? "Completada" : "Pendiente" }}
                        </span>

                        <span class="text-sm text-gray-500">
                          {{ formatDate(task.created_at) }}
                        </span>
                      </div>

                      <div class="mt-4 flex flex-wrap gap-2">
                        <span
                          v-if="task.keywords && task.keywords.length"
                          v-for="kw in task.keywords"
                          :key="kw.id"
                          class="inline-flex items-center px-3 py-1 rounded-full text-xs font-medium bg-blue-100 text-blue-800"
                        >
                          {{ kw.name }}
                        </span>
                        <span v-else class="text-sm text-gray-400 italic"
                          >Sin palabras clave</span
                        >
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Botón de opciones -->
                <div class="relative ml-4">
                  <button
                    @click="toggleMenu(task.id)"
                    class="p-2 rounded-full hover:bg-gray-200 transition-all"
                  >
                    <svg
                      class="w-6 h-6 text-gray-600"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                      viewBox="0 0 24 24"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M6 12h.01M12 12h.01M18 12h.01"
                      />
                    </svg>
                  </button>

                  <!-- Menú desplegable -->
                  <transition name="fade-slide">
                    <div
                      v-if="openMenuId === task.id"
                      class="absolute right-0 mt-2 w-36 bg-white rounded-lg shadow-lg border border-gray-200 z-10 transform transition-all duration-200"
                    >
                      <button
                        @click="startEdit(task)"
                        class="block w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-blue-50 hover:text-blue-600"
                      >
                        ✏️ Editar
                      </button>
                      <button
                        @click="deleteTask(task.id)"
                        class="block w-full text-left px-4 py-2 text-sm text-red-600 hover:bg-red-50"
                      >
                        🗑️ Eliminar
                      </button>
                    </div>
                  </transition>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Footer -->
      <div class="text-center mt-8 text-gray-200 text-sm">
        Prueba Tecnica | Sebastian Guzman &copy; 2025 - Desarrollado con Laravel
        + Vue.js
      </div>
    </div>
    <!-- Modal de edición -->
    <transition name="fade-zoom">
      <div
        v-if="editMode"
        class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50"
      >
        <div
          class="bg-white p-6 rounded-xl shadow-xl w-[420px] transform transition-all duration-300"
        >
          <h3 class="text-lg font-semibold text-gray-800 mb-4">Editar Tarea</h3>

          <!-- Editar título -->
          <label class="block text-sm font-medium text-gray-600 mb-1"
            >Título</label
          >
          <input
            v-model="editTitle"
            type="text"
            class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 mb-4"
            placeholder="Nuevo título"
          />

          <!-- Editar tecnologías -->
          <label class="block text-sm font-medium text-gray-600 mb-2">
            Tecnologías / Palabras clave
          </label>

          <div class="flex items-center mb-3">
            <input
              v-model="newKeyword"
              type="text"
              class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-400"
              placeholder="Agregar tecnología..."
              @keyup.enter="addEditKeyword"
            />
            <button
              @click="addEditKeyword"
              class="ml-2 px-3 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition"
            >
              +
            </button>
          </div>

          <!-- Lista de tecnologías -->
          <div class="flex flex-wrap gap-2 mb-4">
            <span
              v-for="kw in editKeywords"
              :key="kw.id"
              class="inline-flex items-center px-3 py-1 rounded-full text-sm bg-blue-100 text-blue-800"
            >
              {{ kw.name }}
              <button
                @click="removeEditKeyword(kw.id)"
                class="ml-2 text-blue-500 hover:text-red-600 transition"
              >
                ✖
              </button>
            </span>
            <span
              v-if="!editKeywords.length"
              class="text-sm text-gray-400 italic"
            >
              Sin tecnologías aún
            </span>
          </div>

          <!-- Botones de acción -->
          <div class="mt-4 flex justify-end space-x-3">
            <button
              @click="editMode = false"
              class="px-4 py-2 rounded-lg bg-gray-200 text-gray-700 hover:bg-gray-300"
            >
              Cancelar
            </button>
            <button
              @click="confirmEdit"
              class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700"
            >
              Guardar
            </button>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";

// --- Datos simulados ---
const keywords = ref([
  { id: 1, name: "Vue.js" },
  { id: 2, name: "Laravel" },
  { id: 3, name: "MySQL" },
  { id: 4, name: "JavaScript" },
  { id: 5, name: "Tailwind" },
  { id: 6, name: "API REST" },
  { id: 7, name: "Node.js" },
  { id: 8, name: "React" },
  { id: 9, name: "Python" },
  { id: 10, name: "MongoDB" },
]);

const tasks = ref(JSON.parse(localStorage.getItem("tasks") || "[]"));
const newTitle = ref("");
const selectedKeywords = ref([]);
const tempKeyword = ref("");
const loading = ref(false);
const error = ref("");

// --- Computadas ---
const totalTasks = computed(() => tasks.value.length);
const completedTasks = computed(
  () => tasks.value.filter((t) => t.is_done).length
);
const pendingTasks = computed(
  () => tasks.value.filter((t) => !t.is_done).length
);

const availableKeywords = computed(() =>
  keywords.value.filter((k) => !selectedKeywords.value.includes(k.id))
);

const popularKeywords = computed(() => {
  const names = [
    "Vue.js",
    "Laravel",
    "MySQL",
    "JavaScript",
    "Tailwind",
    "API REST",
  ];
  return keywords.value.filter((k) => names.includes(k.name));
});

const frontendKeywords = computed(() => {
  const names = [
    "Vue.js",
    "JavaScript",
    "TypeScript",
    "HTML5",
    "CSS3",
    "Tailwind",
    "Bootstrap",
    "React",
    "SASS",
  ];
  return keywords.value.filter((k) => names.includes(k.name));
});

const backendKeywords = computed(() => {
  const names = [
    "Laravel",
    "PHP",
    "API REST",
    "Node.js",
    "Express",
    "Python",
    "Django",
    "Java",
    "Spring Boot",
  ];
  return keywords.value.filter((k) => names.includes(k.name));
});

const databaseKeywords = computed(() => {
  const names = [
    "MySQL",
    "PostgreSQL",
    "MongoDB",
    "SQLite",
    "Redis",
    "Firebase",
  ];
  return keywords.value.filter((k) => names.includes(k.name));
});

// --- Funciones principales ---
const saveTasks = () =>
  localStorage.setItem("tasks", JSON.stringify(tasks.value));

const fetchTasks = () => {
  // ya no usa backend, solo recarga desde localStorage
  tasks.value = JSON.parse(localStorage.getItem("tasks") || "[]");
};

const createTask = () => {
  error.value = "";
  if (!newTitle.value.trim()) {
    error.value = "El título es obligatorio";
    return;
  }

  loading.value = true;
  setTimeout(() => {
    const newTask = {
      id: Date.now(),
      title: newTitle.value.trim(),
      is_done: false,
      created_at: new Date().toISOString(),
      keywords: keywords.value.filter((k) =>
        selectedKeywords.value.includes(k.id)
      ),
    };

    tasks.value.unshift(newTask);
    saveTasks();

    newTitle.value = "";
    selectedKeywords.value = [];
    loading.value = false;
  }, 400);
};

const toggle = (id) => {
  const task = tasks.value.find((t) => t.id === id);
  if (task) {
    task.is_done = !task.is_done;
    saveTasks();
  }
};

const formatDate = (dateString) =>
  new Date(dateString).toLocaleDateString("es-ES", {
    day: "numeric",
    month: "short",
    year: "numeric",
  });

const getKeywordName = (keywordId) => {
  const k = keywords.value.find((k) => k.id === keywordId);
  return k ? k.name : "";
};

const addKeyword = () => {
  if (
    tempKeyword.value &&
    !selectedKeywords.value.includes(tempKeyword.value)
  ) {
    selectedKeywords.value.push(tempKeyword.value);
  }
  tempKeyword.value = "";
};

const removeKeyword = (keywordId) => {
  selectedKeywords.value = selectedKeywords.value.filter(
    (id) => id !== keywordId
  );
};

const toggleKeyword = (keywordId) => {
  if (selectedKeywords.value.includes(keywordId)) {
    removeKeyword(keywordId);
  } else {
    selectedKeywords.value.push(keywordId);
  }
};

// --- Montaje ---
onMounted(fetchTasks);

// --- Guarda automáticamente en localStorage cuando cambian las tareas ---
watch(tasks, saveTasks, { deep: true });
const openMenuId = ref(null);
const editMode = ref(false);
const editTaskId = ref(null);
const editTitle = ref("");

// --- Control del menú ---
const toggleMenu = (id) => {
  openMenuId.value = openMenuId.value === id ? null : id;
};

// --- Eliminar tarea ---
const deleteTask = (id) => {
  tasks.value = tasks.value.filter((t) => t.id !== id);
  saveTasks();
  openMenuId.value = null;
};

const editKeywords = ref([]); // Palabras clave del modal
const newKeyword = ref(""); // Nueva palabra clave

// Al abrir el modal, cargamos las palabras actuales
const startEdit = (task) => {
  editMode.value = true;
  editTaskId.value = task.id;
  editTitle.value = task.title;
  editKeywords.value = [...(task.keywords || [])]; // Copia las keywords existentes
  newKeyword.value = "";
  openMenuId.value = null;
};

// Agregar nueva palabra clave
// Agregar nueva palabra clave en el modal
const addEditKeyword = () => {
  const name = newKeyword.value.trim();
  if (name && !editKeywords.value.some((k) => k.name === name)) {
    editKeywords.value.push({ id: Date.now(), name });
    newKeyword.value = "";
  }
};

// Eliminar palabra clave en el modal
const removeEditKeyword = (id) => {
  editKeywords.value = editKeywords.value.filter((k) => k.id !== id);
};

// Confirmar edición completa
const confirmEdit = () => {
  const task = tasks.value.find((t) => t.id === editTaskId.value);
  if (task && editTitle.value.trim()) {
    task.title = editTitle.value.trim();
    task.keywords = [...editKeywords.value];
    saveTasks();
  }
  editMode.value = false;
  editTaskId.value = null;
  editTitle.value = "";
  editKeywords.value = [];
};
</script>

<style scoped>
select[multiple]::-webkit-scrollbar {
  width: 8px;
}

select[multiple]::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 0 8px 8px 0;
}

select[multiple]::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 4px;
}

select[multiple]::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}
</style>
