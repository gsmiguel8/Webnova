# Práctica 4: Ramas y conflictos en Git

**Proyecto:** WebNova
**Autor:** Miguel García

---

## 1. Preparar el proyecto

Creamos la carpeta, ponemos `main` como rama por defecto e iniciamos el repositorio.

```bash
mkdir webnova
cd webnova
git config --global init.defaultBranch main
git init
```

Dentro creamos un `index.html` muy simple:

```html
<h1>Bienvenido a WebNova</h1>
<p>Versión inicial del proyecto</p>
```

Y hacemos el primer commit:

```bash
git add .
git commit -m "C1: Versión inicial de WebNova"
```

---

## 2. Trabajar en paralelo

Vamos a simular que tres personas trabajan a la vez, cada una en una parte de la web. Creamos una rama para cada una:

```bash
git branch cabecera
git branch contenido
git branch pie
git branch
```

El último comando  marca `main` con un asterisco

```text
  cabecera
  contenido
* main
  pie
```

![Salida de git branch con las tres ramas creadas](webnova/imagenes/branchs.png)

### Rama `cabecera`

```bash
git switch cabecera
```

Creamos `header.html`:

```html
<header>
    <h2>WebNova - Innovación en la web</h2>
    <nav>
        <a href="#">Inicio</a> | <a href="#">Servicios</a> | <a href="#">Contacto</a>
    </nav>
</header>
```

```bash
git add header.html
git commit -m "C2: Añadido encabezado con navegación"
```

![Salida de git branch con las tres ramas creadas](webnova/imagenes/2commit.png)

### Rama `contenido`

```bash
git switch contenido
```

Creamos `content.html`:

```html
<main>
    <h3>Nuestros servicios</h3>
    <p>Ofrecemos soluciones de desarrollo web a medida.</p>
</main>
```

```bash
git add content.html
git commit -m "C3: Creado contenido principal"
```

![Salida de git branch con las tres ramas creadas](webnova/imagenes/2commit.png)

### Rama `pie`

```bash
git switch pie
```

Creamos `footer.html`:

```html
<footer>
    <p>&copy; 2026 WebNova. Todos los derechos reservados.</p>
</footer>
```

```bash
git add footer.html
git commit -m "C4: Añadido pie de página"
```
![Salida de git branch con las tres ramas creadas](webnova/imagenes/cap3.png)

---

## 3. Juntarlo todo (sin conflictos)

Volvemos a `main` y fusionamos las tres ramas:

```bash
git switch main
git merge cabecera
git merge contenido
git merge pie
```

![Merge de cabecera (fast-forward) contenido y pie](webnova/imagenes/cap4.png)



Cada fusión es de un tipo distinto:

| Rama | Tipo | 
|------|------|
| `cabecera` | Fast-forward |
| `contenido` | 3-way merge | 
| `pie` | 3-way merge |

No hay conflictos porque cada rama tocó un archivo diferente.


### Todo en un solo `index.html`

Copiamos el contenido de los tres archivos dentro de `index.html`:

```html
<h1>Bienvenido a WebNova</h1>
<p>Versión inicial del proyecto</p>

<header>
    <h2>WebNova - Innovación en la web</h2>
    <nav>
        <a href="#">Inicio</a> | <a href="#">Servicios</a> | <a href="#">Contacto</a>
    </nav>
</header>

<main>
    <h3>Nuestros servicios</h3>
    <p>Ofrecemos soluciones de desarrollo web a medida.</p>
</main>

<footer>
    <p>&copy; 2026 WebNova. Todos los derechos reservados.</p>
</footer>
```

```bash
git commit -a -m "C5: Integrados cabecera, contenido y pie en index.html"
```
![Salida de git branch con las tres ramas creadas](webnova/imagenes/cap6.png)

> El `-a` solo mete archivos que Git ya conoce. Como `index.html` está desde C1, no hace falta el `git add`.

### Borrar las ramas que ya no hacen falta

```bash
git branch -d cabecera contenido pie
```

![Salida de git branch con las tres ramas creadas](webnova/imagenes/cap8.png)
---

## 4. Provocar un conflicto

Cambiar la **misma línea** desde dos ramas distintas.

### En la rama `rediseño`

```bash
git switch -c rediseño
```

Cambiamos el `<h1>` de `index.html`:

```html
<h1>WebNova - Sitio oficial (Rediseño 2026)</h1>
```

```bash
git commit -a -m "C6: Rediseño del título principal"
```

### Mientras tanto, en `main`

```bash
git switch main
```

Un compañero cambia esa misma línea:

```html
<h1>WebNova - Portal principal corporativo</h1>
```

```bash
git commit -a -m "C7: Cambio de título corporativo en main"
```



### Fusionamos y salta el conflicto

```bash
git merge rediseño
```

```text
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```



![commit 6, 7 y Mensaje de CONFLICT en la terminal](webnova/imagenes/cap7.png)

Las dos ramas cambiaron la misma línea, y Git no sabe con cuál quedarse. Con `git status` puedes ver qué archivos están en conflicto.

### Qué significan las marcas

Si abres `index.html` verás algo así:

```html
<h1>WebNova - Portal principal corporativo</h1>
```

- `<<<<<<< HEAD`: aquí empieza el bloque. Lo de debajo es la versión de la rama en la que estás (`main`).
- `=======`: separa las dos versiones.
- `>>>>>>> rediseño`: aquí acaba el bloque. Lo de arriba es lo que viene de la rama que fusionamos.

![index.html con las marcas de conflicto abiertas en el editor](capturas/06-conflicto-editor.png)

### Resolverlo a mano

Borramos las tres marcas y dejamos una línea que mezcle las dos ideas:

```html
<h1>WebNova - Portal principal (Rediseño 2026)</h1>
```

```bash
git add index.html
git commit -m "C8: Conflicto resuelto entre main y rediseño en el H1"
```

---

## 5. Ver el historial

```bash
git log --oneline --graph --all
```

![Conflicto resuelto, commit C8 hecho y grafo historial](webnova/imagenes/cap9.png)

La rama `rediseño` sigue ahí porque no la hemos borrado. Si quisieras limpiarla: `git branch -d rediseño`.

---

## 6. Resumen de commits

| Commit | Rama | Qué hace |
|--------|------|----------|
| C1 | `main` | Versión inicial |
| C2 | `cabecera` | Encabezado con navegación |
| C3 | `contenido` | Contenido principal |
| C4 | `pie` | Pie de página |
| C5 | `main` | Todo unido en `index.html` |
| C6 | `rediseño` | Nueva rama |
| C7 | `main` | Título corporativo |
| C8 | `main` | Merge con el conflicto resuelto |



---

## 7. Lo que me llevo de la práctica

- Una rama por cada cosa que se hace, y se borra cuando ya está fusionada.
- `git switch` para cambios, `git switch -c` crea y cambia de rama de una vez.
- Commits pequeños y con mensajes que se entiendan.
- Para fusionar, desde la rama de destino y con el árbol limpio.
- En un conflicto no hay que elegir una versión, lo mejor es entender qué quería cada cambio y combinarlos.
- Mirar el grafo con `git log --oneline --graph --all` después de cada integración para comprobar el trabajo hasta el momento.




