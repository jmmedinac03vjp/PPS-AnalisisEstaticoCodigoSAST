
# Actividad 1: Análisis de Código Estático con SAST (Static Application Security Testing)

## Tema
Identificación de vulnerabilidades en código fuente.

## Objetivo
Usar SAST para detectar problemas de seguridad en el código sin ejecutarlo.

---

## ¿Qué es SAST?

**SAST** (Static Application Security Testing) es una técnica que permite analizar el código fuente de una aplicación para detectar vulnerabilidades **antes de su despliegue**. No requiere ejecutar el software, lo que permite encontrar errores de forma temprana en el ciclo de desarrollo.

---

## ¿Qué es Semgrep?

**Semgrep** (Security Enhanced Multi-Grep) es una herramienta de análisis estático de código fuente. A diferencia de `grep`, Semgrep entiende la estructura del código y es capaz de detectar patrones de vulnerabilidades y malas prácticas.

### Ventajas:
- ✅ Detecta vulnerabilidades automáticamente
- 🔁 Se puede integrar en pipelines CI/CD
- 🌐 Soporta múltiples lenguajes (JavaScript, Python, Go, Java, etc.)
- 🛠️ Permite definir reglas personalizadas

---

## Instalación de Semgrep en entorno virtual

1. Crear un entorno virtual:
```bash
python3 -m venv semgrep_env
```

2. Activarlo:
```bash
source semgrep_env/bin/activate
```

3. Instalar Semgrep:
```bash
pip install semgrep
```
Si tuvieras algún problema con la instalación con `pip` puedes probar con snap:

```bash
sudo apt install snap
snap install semgrep
```


4. Comprobar instalación:
```bash
semgrep --version
```

5. Para salir del entorno:
```bash
deactivate
```

---

## Análisis con Semgrep sobre NodeGoat

### ¿Qué es NodeGoat?

[NodeGoat](https://github.com/OWASP/NodeGoat) es una aplicación vulnerable creada para prácticas de seguridad con Node.js y MongoDB.

### Vulnerabilidades incluidas:
- Inyección MongoDB
- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- Almacenamiento inseguro de contraseñas
- Fallos de autenticación
- Mal uso de funciones de Node.js

### Clonar y acceder al proyecto:
```bash
git clone https://github.com/OWASP/NodeGoat.git
cd NodeGoat
```

---

## Ejecutar Semgrep

### Instalación rápida:
```bash
pip install semgrep
```

### Análisis automático del código:
```bash
semgrep --config=auto .
```

### Resultado:
- Muestra archivo y línea
- Tipo de vulnerabilidad
- Recomendación

---

## Reglas OWASP Top 10

Puedes usar reglas específicas del proyecto OWASP con:
```bash
semgrep --config "p/owasp-top-ten" .
```

---

## Ejemplo: Código vulnerable vs seguro

### ❌ Código vulnerable:
```javascript
db.query("SELECT * FROM users WHERE username = '" + user + "'");
```

### ✅ Código corregido:
```javascript
db.query("SELECT * FROM users WHERE username = ?", [user]);
```

---

## CI/CD con Semgrep

### ¿Qué es CI/CD?

- **CI (Integración Continua)**: Automatiza pruebas tras cada cambio.
- **CD (Entrega / Despliegue Continuo)**:
  - Entrega: preparado para producción.
  - Despliegue: se lanza automáticamente.

### Herramientas comunes:

| CI             | CD             |
|----------------|----------------|
| GitHub Actions | Docker         |
| GitLab CI/CD   | Kubernetes     |
| Jenkins        | AWS CodeDeploy |
| Travis CI      | ArgoCD         |

---

## Integración de Semgrep en GitHub Actions

Crear archivo: `.github/workflows/semgrep.yml`

```yaml
name: Semgrep SAST Scan

on:
  push:
    branches:
      - main
      - develop
  pull_request:

jobs:
  semgrep:
    runs-on: ubuntu-latest

    steps:
      - name: Clonar repositorio
        uses: actions/checkout@v4

      - name: Instalar Semgrep
        run: pip install semgrep

      - name: Ejecutar análisis
        run: semgrep --config=auto --json --output=semgrep-results.json

      - name: Mostrar resultados
        run: cat semgrep-results.json

      - name: Guardar reporte
        uses: actions/upload-artifact@v3
        with:
          name: semgrep-report
          path: semgrep-results.json
```

---

## Crear reglas personalizadas

Archivo: `semgrep-rules.yaml`

```yaml
rules:
  - id: detect-insecure-sql
    patterns:
      - pattern: db.query("SELECT * FROM users WHERE username = " + $X)
    message: "Posible inyección SQL detectada"
    languages: [javascript]
    severity: ERROR
```

### Uso de reglas personalizadas:
```bash
semgrep --config=semgrep-rules.yaml --json --output=semgrep-results.json
```

---

## Integración con GitLab CI/CD

Crear archivo: `.gitlab-ci.yml`

```yaml
stages:
  - security

semgrep-sast:
  image: python:3.10
  stage: security
  before_script:
    - pip install semgrep
  script:
    - semgrep --config=auto --json --output=semgrep-results.json
  artifacts:
    paths:
      - semgrep-results.json
```

---

## Reglas personalizadas en GitLab

Archivo: `custom-rules.yaml`

```yaml
rules:
  - id: detect-insecure-sql
    patterns:
      - pattern: db.query("SELECT * FROM users WHERE username = " + $X)
    message: "Posible inyección SQL detectada"
    languages: [javascript]
    severity: ERROR
```

### Ejecutar análisis con las reglas:
```bash
semgrep --config=custom-rules.yaml .
```

---

## Recursos

- [Semgrep Documentation](https://semgrep.dev/docs/)
- [OWASP NodeGoat](https://github.com/OWASP/NodeGoat)
- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)

---

## Conclusión

Usar herramientas como Semgrep permite detectar vulnerabilidades desde las primeras etapas del desarrollo, reduciendo costos y mejorando la calidad y seguridad del software. Integrar SAST en pipelines CI/CD es una buena práctica profesional recomendada por OWASP y la industria.

---
