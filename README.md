<table width="100%">
  <tr>
    <div align="center">
      <h1>🧠 Transfer Learning Masterclass</h1>
    </div>
  </tr>
</table>

> 🎓 **Repositorio académico** | Bootcamp
> 📌 Módulo: Aprendizaje Profundo / Visión por Computador — Transfer Learning & Fine-Tuning
> 🎯 Objetivos: Maximizar precisión y reusar arquitecturas en clasificación de imágenes

¿Quieres entrenar modelos de Inteligencia Artificial potentes sin gastar semanas ni miles de dólares en cómputo? De eso trata **Transfer Learning**: reutilizar el conocimiento previamente adquirido por redes neuronales avanzadas para resolver nuevos problemas con pocos datos y en poco tiempo.

---

## 📁 ¿Qué hay en este repositorio?
```
Transfer-Learning-Masterclass/
├── 📂 docs/
│   ├── TransferLearningPresentacion.pdf                           ← Diapositiva teórica de la clase
│   ├── FichaCriterioTransferLearning.pdf                          ← Pautas y criterios éticos/metodológicos
│   └── GuiaErroresTransferLearning.pdf                            ← Guía de resolución de fallos comunes
|
├── 📂 image/                                                      ← Diagramas de arquitecturas y matrices
|   ├── arquitectura_mobilenetv2.png
|   └── confusion_matrix_comparison.png 
|   └── feature_extraction_schema.png
|   └── fine_tuning_schema.png
|                                           
├── 📂 notebooks/
│   ├── 01_Introduccion_Transfer_Learning.ipynb                    ← Carga de MobileNetV2 y estructura base
│   ├── 02_Feature_Extraction.ipynb                                ← Congelado de capas y extracción de
│   ├── 03_Fine_Tuning.ipynb                                       ← Ajuste fino progresivo con LR reducido
│   |── 04_Transfer_Learning_Solutions.ipynb                       ← Evaluación comparativa y matrices de confusión
|   └── 🏭_Actividad_Caso_Practico_Inspeccion_Industrial.ipynb     ← Actividad práctica: inspección de piezas
|
├── 📄 .gitignore                                                  ← Exclusión de cachés y entornos virtuales
|
└── 📄 requirements.txt                                            ← Dependencias necesarias del proyecto
```

---

## 📄 Documentos de la clase

<details>
<summary>📊 <strong>TransferLearningPresentacion.pdf — Presentación Teórica y Caso Conductor</strong></summary>

### 🐱 vs 🐶 Caso Conductor: Clasificar Gatos vs. Perros con MobileNetV2
- **El Problema:** Contar con pocas imágenes (ej. 800 imágenes) genera riesgo de *overfitting*, y entrenar desde cero requiere demasiado tiempo de GPU[cite: 3].
- **La Solución:** En lugar de entrenar una CNN desde cero, reutilizamos las representaciones visuales que la red aprendió con un dataset masivo como ImageNet[cite: 3].

---

### 🧩 Memoria Visual de MobileNetV2
Las capas convolucionales procesan la información de manera jerárquica:
1. **Bordes:** Líneas, contrastes y direcciones básicas[cite: 3].
2. **Texturas:** Pelaje, manchas y superficies[cite: 3].
3. **Formas:** Curvas, ojos, orejas y siluetas[cite: 3].
4. **Patrones de Objeto:** Combinaciones complejas para clasificar objetos completos[cite: 3].

---

### 🧪 Experimentos e Implementación
- **Experimento 1 (Feature Extraction):** Congelamos la base (`base.trainable = False`), añadimos un *GlobalAveragePooling2D* y una capa `Dense(1)` para entrenar solo la nueva cabeza clasificadora[cite: 3].
- **Experimento 2 (Fine-Tuning):** Descongelamos los últimos bloques de MobileNetV2 y reentrenamos la red completa usando un *Learning Rate* muy bajo ($10^{-5}$) para adaptar las características profundas sin destruir lo aprendido[cite: 3].

---

### 🏁 Las 3 Ideas Clave del Cierre
1. **Reutilizar:** Transfer Learning aprovecha representaciones aprendidas en otra tarea visual[cite: 3].
2. **Congelar:** Feature Extraction mantiene la base fija y entrena solo el clasificador nuevo[cite: 3].
3. **Adaptar:** Fine-Tuning descongela parte de la red y ajusta las capas superiores con $LR$ baja[cite: 3].

> 💡 **Regla Práctica:** Empieza siempre con la baseline más segura (Feature Extraction) y descongela capas solo si la validación lo justifica[cite: 3].

</details>

---

<details>
<summary>⚖️ <strong>FichaCriterioTransferLearning.pdf — Fundamentos y Matriz de Decisión</strong></summary>

### 🧠 Memoria Jerárquica de una CNN
- **Capas bajas:** Detectan bordes y texturas; son universales y siempre se congelan[cite: 1].
- **Capas medias:** Capturan formas y patrones parcialmente transferibles[cite: 1].
- **Capas altas:** Representan conceptos específicos del dataset original y requieren reentrenar[cite: 1].

---

### 📊 Matriz de Decisión 2x2
| Dataset | Similar al dominio original | Diferente del dominio original |
| :--- | :--- | :--- |
| **Pequeño** | **Feature Extraction:** Congelar base completa + clasificador propio[cite: 1]. | **Feature Extraction:** Congelar solo capas bajas (1-2 bloques)[cite: 1]. |
| **Grande** | **Fine-Tuning:** Descongelar capas altas (2-3 últimas) con $LR$ baja ($10^{-5}$)[cite: 1, 2]. | **Fine-Tuning Agresivo:** Descongelar más capas (4-5 bloques) con $LR$ muy baja[cite: 1]. |

---

### 🔄 Flujo de Trabajo
1. **Preparar datos:** Preprocesamiento con la normalización propia del modelo base[cite: 1].
2. **Feature Extraction:** Cargar base, congelar (`trainable=False`), añadir clasificador y entrenar[cite: 1].
3. **Evaluar:** Validar métricas en el conjunto de validación[cite: 1].
4. **Fine-Tuning:** Descongelar capas altas, reducir $LR$, recompilar obligatoriamente y reentrenar[cite: 1].

</details>

---

<details>
<summary>🚑 <strong>GuiaErroresTransferLearning.pdf — Diagnóstico Rápido y Errores Comunes</strong></summary>

### 🛠️ Diagnóstico Rápido de Errores

| Problema | Señal Principal en Curvas | Primera Acción |
| :--- | :--- | :--- |
| **Catastrophic Forgetting** | Caída brusca de `val_accuracy` tras iniciar Fine-Tuning[cite: 2]. | Reducir $LR$ ($10^{-5}$) y descongelar progresivamente[cite: 2]. |
| **Overfitting** | `train_loss` baja mientras `val_loss` sube[cite: 2]. | Feature Extraction + Dropout(0.5) + Data Augmentation[cite: 1, 2]. |
| **Preprocesamiento Incorrecto** | El modelo no converge desde la primera época[cite: 2]. | Usar la función `preprocess_input` propia de la red base[cite: 2]. |
| **Mal uso de BatchNorm** | Métricas inestables o pobres en test/evaluación[cite: 2]. | Fijar `base_model(inputs, training=False)` al construir[cite: 2]. |
| **Olvidar Recompilar** | El modelo no aprende nada nuevo tras descongelar[cite: 2]. | Ejecutar `model.compile()` inmediatamente tras cambiar `.trainable`[cite: 2]. |

---

### ⚡ 4 Reglas de Oro
1. **Empieza siempre con Feature Extraction** antes de hacer Fine-Tuning[cite: 1, 2].
2. **Usa un Learning Rate bajo** ($10^{-5}$ o menor) durante el Fine-Tuning[cite: 1, 2].
3. **Recompila siempre el modelo** tras modificar el estado de `.trainable`[cite: 1, 2].
4. **Monitorea las curvas de validación** para detectar desajustes a tiempo[cite: 2].

</details>

---

## 📓 Notebooks de práctica

<details>
<summary>📋 <strong>Flujo de trabajo de los Notebooks</strong></summary>

| Notebook | Propósito | Entregables / Salidas |
|----------|-----------|------------------------|
| 📘 `01_Introduccion_Transfer_Learning.ipynb` | Carga de MobileNetV2 (ImageNet) y acople del clasificador (`GlobalAveragePooling2D` + `Dense`). | `image/arquitectura_mobilenetv2.png` |
| ⚙️ `02_Feature_Extraction.ipynb` | Extracción de características congelando el modelo base (`base_model.trainable = False`). | `image/feature_extraction_schema.png` |
| 🔧 `03_Fine_Tuning.ipynb` | Ajuste fino progresivo descongelando capas superiores (>100) y ajustando el learning rate a `1e-5`. | `image/fine_tuning_schema.png` |
| 📊 `04_Transfer_Learning_Solutions.ipynb` | Evaluación comparativa entre ambas técnicas mediante matrices de confusión y métricas de *accuracy*. | `image/confusion_matrix_comparison.png` |
| 🏭 `🏭_Actividad_Caso_Practico_Inspeccion_Industrial.ipynb` | Caso práctico de inspección de piezas (*Conforme* vs *Defectuosa*) para completar ejercicios `TODO`. | Resolución completa del flujo |

</details>

---

## 🗓️ ¿Cómo seguir la clase paso a paso?

1. 🎬 PASO 1 — Revisar el marco conceptual    → docs/TransferLearningPresentacion.pdf
2. 📐 PASO 2 — Consultar buenas prácticas     → docs/FichaCriterioTransferLearning.pdf
3. 💻 PASO 3 — Ejecutar notebooks guía       → notebooks/01 al 04 + revisar carpeta image/
4. 🚑 PASO 4 — Consultar solución de errores  → docs/GuiaErroresTransferLearning.pdf
5. 🎯 PASO 5 — Resolver el reto práctico     → notebooks/🏭_Actividad_Caso_Practico_Inspeccion_Industrial.ipynb
