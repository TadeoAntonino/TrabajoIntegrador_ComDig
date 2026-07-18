## Requisitos

- **Python 3.10 o superior**
- Librerías: `numpy`, `matplotlib`, `scipy`, `jupyter`, `ipykernel`
- Para el notebook `05b_SDR_Real.ipynb` además: `pyadi-iio` (solo necesario si
  se va a correr con el hardware PlutoSDR conectado, por ejemplo en el
  JupyterHub del laboratorio)

## Instalación

### 1. Clonar el repositorio

```bash
git clone <URL-del-repositorio>
cd <carpeta-del-repositorio>
```

### 2. Crear un entorno virtual

**En Windows**, el ejecutable de Python puede llamarse `python` o `py` según
la instalación. Verificalo antes de continuar:

```powershell
python --version
py --version
```

Usá el que te devuelva una versión 3.10+ (si `python` apunta a Python 2.x,
usá `py` en su lugar). Con eso confirmado, creá el entorno virtual:

```powershell
py -m venv venv
```

**En macOS/Linux:**

```bash
python3 -m venv venv
```

### 3. Activar el entorno virtual

**Windows (PowerShell):**

```powershell
.\venv\Scripts\Activate
```

> Si PowerShell devuelve un error de permisos al activar ("no se puede cargar
> el archivo... porque la ejecución de scripts está deshabilitada"), correr
> una vez antes de activar:
> ```powershell
> Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
> ```

**macOS/Linux:**

```bash
source venv/bin/activate
```

Al activarse correctamente, la terminal debería mostrar `(venv)` al
principio de la línea.

### 4. Instalar las dependencias

```bash
pip install numpy matplotlib scipy jupyter ipykernel
```

Para el notebook de SDR real (solo si se va a correr con el PlutoSDR
conectado):

```bash
pip install pyadi-iio
```

#### Nota para Windows — error de "Long Path" durante la instalación

Si la instalación falla con un error del estilo:

```
OSError: [Errno 2] No such file or directory: '...archivo con nombre muy largo...'
HINT: This error might have occurred since this system does not have
Windows Long Path support enabled.
```

Es un límite de Windows a la longitud de las rutas de archivo (260
caracteres), y algunos paquetes de Jupyter lo superan. Para solucionarlo:

1. Abrir PowerShell **como administrador**
2. Ejecutar:
   ```powershell
   New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
   ```
3. Reiniciar la PC
4. Repetir la instalación del paso 4

## Uso con Visual Studio Code

1. Instalar las extensiones **Python** y **Jupyter** (ambas de Microsoft)
   desde el marketplace de VS Code.
2. Abrir la carpeta del proyecto en VS Code.
3. Abrir cualquiera de los notebooks (`.ipynb`).
4. En la esquina superior derecha del notebook, seleccionar como **kernel**
   el entorno virtual creado (debería listarse como `venv (Python 3.x.x)`).
   Si no aparece automáticamente, usar "Select Another Kernel" → "Python
   Environments" y buscar manualmente `venv\Scripts\python.exe`.
5. Ejecutar las celdas con `Shift + Enter`, o usar el botón **"Run All"**
   para correr el notebook completo de una vez.