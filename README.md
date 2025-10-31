# AgenteIA - Sistema de Análisis Inteligente de Licitaciones

Sistema avanzado de análisis automatizado de licitaciones públicas que utiliza Inteligencia Artificial y agentes especializados para evaluar propuestas contractuales, detectar riesgos y recomendar la mejor opción basándose en criterios legales, técnicos y económicos.

## Descripción General

AgenteIA es una plataforma completa que combina múltiples tecnologías de IA para automatizar el proceso de análisis y evaluación de contratos en licitaciones públicas. El sistema utiliza:

- **Agentes de IA especializados** para validar diferentes aspectos de las propuestas
- **RAG (Retrieval-Augmented Generation)** para comparación contextual con documentos de referencia
- **Procesamiento de lenguaje natural** para extracción y análisis de información
- **Validación automatizada de RUC** para verificar empresas y detectar riesgos empresariales
- **Sistema de puntuación ponderada** para comparación objetiva de propuestas

## Características Principales

### 1. Análisis Multi-Dimensional con IA
El sistema evalúa cada propuesta desde múltiples perspectivas utilizando agentes especializados:

- **Validador Legal**: Analiza garantías, multas, plazos y cumplimiento normativo
- **Validador Técnico**: Evalúa especificaciones técnicas, materiales, procesos y tiempos
- **Validador Económico**: Examina estructura de precios, forma de pago y viabilidad financiera
- **Validador de Inconsistencias**: Detecta contradicciones, ambigüedades y vacíos legales
- **Validador de RUC**: Verifica empresas y detecta relaciones de riesgo

### 2. RAG (Retrieval-Augmented Generation)
El sistema utiliza ChromaDB para indexar y recuperar información relevante:

- Indexación automática de pliegos y documentos legales de referencia
- Búsqueda semántica por tópicos específicos (garantías, multas, plazos, etc.)
- Comparación contextual entre propuestas y requisitos del pliego
- Embeddings generados con modelos de OpenAI (text-embedding-3-small)

### 3. Sistema de Comparación Inteligente
- Puntuación ponderada configurable por licitación
- Comparación automática entre múltiples propuestas
- Identificación del ganador basada en análisis integral
- Generación de justificación automática con IA explicando la decisión

### 4. Detección de Riesgos
Clasificación automática de riesgos en tres niveles:
- **ALTO/ROJO**: Incumplimientos críticos o riesgos severos
- **MEDIO/AMARILLO**: Advertencias que requieren atención
- **BAJO**: Cumplimiento adecuado

### 5. Chat Interactivo con IA
- Interfaz conversacional para consultar sobre los análisis
- Respuestas contextuales basadas en los reportes generados
- Acceso a evidencia específica de los documentos
- Explicaciones claras y educativas

## Arquitectura Técnica

### Backend (FastAPI + Python)

#### Componentes Principales

**API REST** (`app.py`):
- Gestión de licitaciones (CRUD completo)
- Subida y procesamiento de documentos PDF
- Orquestación del análisis con agentes
- Generación de reportes en JSON y PDF
- Sistema de chat con IA

**Agentes de Validación**:
- `validator_legal.py`: Análisis de aspectos legales
- `validator_tech.py`: Evaluación técnica
- `validator_econ.py`: Análisis económico
- `validator_incons.py`: Detección de inconsistencias
- `validator_ruc.py`: Validación de empresas
- `aggregator.py`: Consolidación de resultados

**Sistema RAG**:
- `chroma_setup.py`: Configuración de ChromaDB
- `ingest_legal_docs.py`: Indexación de documentos legales de referencia
- `ingest_proposals.py`: Indexación de propuestas
- `retrieve.py`: Recuperación semántica de información
- `rag_legal.py`: Orquestación de búsquedas por tópicos

**Utilidades**:
- `pdf_text.py`: Extracción de texto desde PDFs con PyPDF
- `ruc_extract.py`: Extracción de RUCs mediante expresiones regulares
- `chunk.py`: Segmentación de documentos para embeddings

### Frontend (React + TypeScript + Vite)

Interfaz moderna construida con:
- **React 18** y **TypeScript**
- **Vite** para bundling optimizado
- **shadcn/ui** componentes de UI (Radix UI)
- **TailwindCSS** para estilos
- **React Query** para gestión de estado del servidor
- **React Router** para navegación
- **Recharts** para visualizaciones

## Flujo de Análisis con IA

### 1. Ingesta y Preparación
```
Usuario sube PDFs → Sistema extrae texto → Clasifica (pliego/propuesta)
                                          ↓
                              Genera chunks semánticos
                                          ↓
                              Crea embeddings con OpenAI
                                          ↓
                              Indexa en ChromaDB
```

### 2. Análisis por Agentes
Para cada propuesta:

```
Propuesta PDF → Extracción de texto
                      ↓
         ┌────────────┴────────────┐
         │                         │
    RAG Search              Análisis Directo
    (6 tópicos)             (RUC, texto completo)
         │                         │
         ↓                         ↓
  ┌─────────────────────────────────────┐
  │  Validadores Paralelos con IA:      │
  │  - Legal (garantías, multas, plazos)│
  │  - Técnico (especificaciones)       │
  │  - Económico (precios, pagos)       │
  │  - Inconsistencias (coherencia)     │
  │  - RUC (empresas, riesgos)          │
  └─────────────┬───────────────────────┘
                ↓
          Aggregator
         (consolida scores + issues)
                ↓
        Reporte Individual
```

### 3. Comparación y Justificación
```
Reportes Individuales → Sistema de Puntuación Ponderada
                                    ↓
                        Ranking de Propuestas
                                    ↓
                    IA genera Justificación
                    (GPT-4 con contexto completo)
                                    ↓
                        Reporte Final
```

### Tópicos de Búsqueda RAG

El sistema busca contexto relevante para 6 categorías:

1. **garantias**: Garantías técnicas, de fiel cumplimiento, de anticipo
2. **multas**: Penalizaciones, sanciones, incumplimientos
3. **plazos**: Tiempos de entrega, cronogramas, hitos
4. **tecnicos**: Especificaciones, materiales, procesos, metodologías
5. **economicos**: Presupuestos, forma de pago, condiciones financieras
6. **coherencia**: Consistencia interna del documento

Para cada tópico, el sistema:
1. Genera una consulta semántica basada en la propuesta
2. Recupera los 6 chunks más relevantes del pliego
3. Combina este contexto con el texto de la propuesta
4. Lo envía al agente validador correspondiente

### Modelos de IA Utilizados

- **Embeddings**: `text-embedding-3-small` (OpenAI)
- **Generación/Análisis**: `gpt-4o-mini` (configurable)
- **Chat**: `gpt-4o-mini` (configurable)

Los agentes reciben el contexto y utilizan prompts especializados para:
- Identificar cumplimientos e incumplimientos
- Asignar scores de 0-100
- Generar issues con evidencia textual
- Clasificar severidad de riesgos

## Instalación

### Requisitos Previos
- Python 3.9 o superior
- Node.js 18 o superior
- Cuenta de OpenAI con API key

### Backend

```bash
cd Hackatho-Agents/AgenteIA

# Crear entorno virtual
python -m venv venv

# Activar entorno virtual
# Windows:
venv\Scripts\activate
# Linux/Mac:
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt

# Configurar variables de entorno
# Crear archivo .env con:
OPENAI_API_KEY=tu_api_key_aqui
MODEL_EMB=text-embedding-3-small
MODEL_JUST=gpt-4o-mini
```

### Frontend

```bash
cd frontend

# Instalar dependencias
npm install

# Configurar variables de entorno
# Crear archivo .env con:
VITE_API_URL=http://localhost:8000
```

## Uso

### Iniciar el Backend

```bash
cd Hackatho-Agents/AgenteIA

# Activar entorno virtual
venv\Scripts\activate  # Windows
# source venv/bin/activate  # Linux/Mac

# Iniciar servidor
python app.py

# El servidor estará disponible en http://localhost:8000
# Documentación API: http://localhost:8000/docs
```

### Iniciar el Frontend

```bash
cd frontend

# Modo desarrollo
npm run dev

# El frontend estará disponible en http://localhost:5173
```

### Flujo de Trabajo

1. **Crear Licitación**:
   - Define nombre, objeto, presupuesto
   - Configura pesos (% legal, técnico, económico)
   - Establece normativa aplicable y deadline

2. **Subir Documentos**:
   - Pliego de condiciones (referencia)
   - Propuestas de oferentes (a evaluar)
   - Los documentos se indexan automáticamente

3. **Ejecutar Análisis**:
   - Clic en "Analizar"
   - El sistema procesa todas las propuestas
   - Genera scores, detecta issues, valida RUCs

4. **Revisar Resultados**:
   - **Resumen**: Justificación automática y KPIs
   - **Hallazgos**: Listado de todos los issues detectados
   - **Validaciones RUC**: Estado de empresas y riesgos
   - **Comparativo**: Tabla con scores y ganador
   - **Chat**: Preguntas interactivas sobre el análisis

5. **Descargar Reporte**:
   - PDF ejecutivo de 2 páginas
   - JSON completo con todos los detalles

### Análisis por Consola

```bash
cd Hackatho-Agents/AgenteIA

# Analizar contratos en carpeta
python analyze_contracts.py "analiza los contratos" "Objeto del contrato"

# Genera: reporte_contratos.json
```

## Estructura del Proyecto

```
├── frontend/                    # Aplicación React
│   ├── src/
│   │   ├── components/         # Componentes UI
│   │   ├── pages/              # Páginas principales
│   │   ├── hooks/              # Custom hooks
│   │   └── lib/                # Utilidades y configs
│   ├── package.json
│   └── vite.config.ts
│
├── Hackatho-Agents/AgenteIA/   # Backend Python
│   ├── agents/                 # Agentes validadores
│   │   ├── validator_legal.py
│   │   ├── validator_tech.py
│   │   ├── validator_econ.py
│   │   ├── validator_incons.py
│   │   ├── validator_ruc.py
│   │   ├── aggregator.py
│   │   ├── justificador.py
│   │   └── rag_legal.py
│   │
│   ├── rag/                    # Sistema RAG
│   │   ├── chroma_setup.py
│   │   ├── ingest_legal_docs.py
│   │   ├── ingest_proposals.py
│   │   └── retrieve.py
│   │
│   ├── utils/                  # Utilidades
│   │   ├── pdf_text.py
│   │   ├── ruc_extract.py
│   │   └── chunk.py
│   │
│   ├── data/                   # Datos
│   │   └── docs/              # PDFs subidos (gitignored)
│   │
│   ├── db/                     # Base de datos JSON
│   │   └── licitaciones.json  # (gitignored)
│   │
│   ├── reports/                # Reportes generados (gitignored)
│   │
│   ├── app.py                  # FastAPI principal
│   ├── analyze_contracts.py    # Script CLI
│   └── requirements.txt        # Dependencias Python
│
├── .gitignore
└── README.md
```

## API Endpoints Principales

### Licitaciones
- `POST /licitaciones` - Crear nueva licitación
- `GET /licitaciones` - Listar todas
- `GET /licitaciones/{id}` - Obtener detalle

### Documentos
- `POST /licitaciones/{id}/documentos` - Subir PDFs
- `GET /licitaciones/{id}/documentos` - Listar documentos
- `POST /licitaciones/{id}/indexar` - Indexar en ChromaDB

### Análisis
- `POST /licitaciones/{id}/analizar` - Ejecutar análisis completo

### Resultados
- `GET /licitaciones/{id}/resumen` - Resumen ejecutivo
- `GET /licitaciones/{id}/hallazgos` - Listado de issues
- `GET /licitaciones/{id}/validaciones/ruc` - Validaciones de RUC
- `GET /licitaciones/{id}/comparativo` - Tabla comparativa

### Interactivo
- `POST /licitaciones/{id}/chat` - Chat con IA
- `GET /licitaciones/{id}/resumen-ejecutivo.pdf` - Descargar PDF

Documentación completa en: `http://localhost:8000/docs`

## Tecnologías

### Backend
- **FastAPI**: Framework web moderno y rápido
- **OpenAI API**: Modelos GPT-4 y embeddings
- **ChromaDB**: Base de datos vectorial para RAG
- **PyPDF**: Extracción de texto de PDFs
- **ReportLab**: Generación de PDFs
- **Pydantic**: Validación de datos
- **Uvicorn**: Servidor ASGI

### Frontend
- **React 18**: Biblioteca UI
- **TypeScript**: Type safety
- **Vite**: Build tool rápido
- **TailwindCSS**: Estilos utility-first
- **shadcn/ui**: Componentes accesibles
- **React Query**: Estado del servidor
- **React Router**: Navegación
- **Recharts**: Gráficos

## Configuración de Pesos

Los pesos determinan la importancia relativa de cada criterio en la puntuación final:

```json
{
  "legal": 35,      // 35% del score total
  "tecnico": 40,    // 40% del score total
  "economico": 25   // 25% del score total
}
```

Los pesos son configurables por licitación y afectan:
- El ranking de propuestas
- El cálculo del ganador
- La justificación generada por IA

## Métricas y Scores

Cada validador genera un score de 0-100:
- **100-85**: Cumplimiento excelente (Riesgo BAJO)
- **84-60**: Cumplimiento aceptable (Riesgo MEDIO)
- **59-0**: Incumplimiento o deficiencias (Riesgo ALTO)

El score total se calcula como:
```
Score Total = (legal × peso_legal) + (técnico × peso_técnico) + (económico × peso_económico)
```

## Contribución

Este proyecto fue desarrollado para el HackIAthon de Viamatica, demostrando el poder de la IA para automatizar procesos complejos de análisis documental y toma de decisiones en el sector público.

## Licencia

Este proyecto es de código abierto bajo licencia MIT.

## Contacto y Soporte

Para preguntas, sugerencias o reportar issues:
- GitHub: [https://github.com/KernyMC/HackIAthon---Viamatica](https://github.com/KernyMC/HackIAthon---Viamatica)

---

Desarrollado con ❤️ utilizando IA para mejorar la transparencia y eficiencia en licitaciones públicas.
