# Detección de acumulación en esquemas de pump & dump usando IPR de la Laplaciana con filtrado RMT

Paper académico en formato IEEE (español) que propone un método para detectar la fase de acumulación en esquemas de *pump & dump* en criptoactivos. El enfoque combina teoría de grafos (MST + redes de correlación), RMT (Random Matrix Theory) para filtrado de ruido, e IPR (Inverse Participation Ratio) de la Laplaciana normalizada como estadístico de detección.

**Autores:** Proyecto de grado, Universidad del Rosario.

## Resumen

Se construye una red de correlaciones entre criptoactivos a partir de retornos históricos, se filtra con RMT (distribución de Marchenko-Pastur), se esparsifica mediante MST con aristas adicionales calibradas por modularidad (algoritmo Leiden), y se analiza el perfil del IPR de la Laplaciana normalizada en ventanas temporales deslizantes. El percentil 90 de la distribución de IPR (IPR<sub>p90</sub>) sirve como estadístico de detección, comparado contra un modelo nulo basado en Configuration Model (10 000 redes) con umbral en el percentil 99.7 empírico.

## Estructura del repositorio

```
├── main.tex                 % Documento principal IEEE
├── sec/
│   ├── abstract.tex         % Resumen
│   ├── intro.tex            % Introducción
│   ├── problema.tex         % Planteamiento del problema
│   ├── marco_teorico.tex    % Marco teórico
│   ├── modelamiento.tex     % Modelamiento
│   ├── pipeline.tex         % Pipeline metodológico
│   ├── solucion.tex         % Solución propuesta
│   ├── objetivos.tex        % Objetivos
│   └── referencias.tex      % Referencias
├── codigo/                  % Implementación / experimentos
├── cp/                      % Carpetas de respaldo
├── recursos/                % PDFs de referencias
├── CONTEXTO.md              % Contexto del proyecto para agentes IA
├── AGENTS.md                % Guía para agentes IA sobre este proyecto
└── README.md                % Este archivo
```

## Metodología

1. **Construcción de red:** Correlación de Pearson entre pares SYM/BTC en ventanas de 48 h.
2. **Filtrado RMT:** Eliminación de eigenvalores por debajo de $\lambda_+$ (límite de Marchenko-Pastur).
3. **Esparsificación:** MST + aristas adicionales calibradas por modularidad (Leiden).
4. **IPR sobre Laplaciana normalizada:** Cálculo del IPR por eigenvector en $[0,2]$.
5. **Detección:** IPR<sub>p90</sub> vs. Configuration Model (10 000 redes), umbral en percentil 99.7.
6. **Métrica principal:** PR-AUC (clases desbalanceadas).

## Dataset

~351 eventos de pump & dump identificados por La Morgia et al. (2020), con pares SYM/BTC en Binance durante 2021–2022.

## Licencia

Proyecto académico — Universidad del Rosario.
