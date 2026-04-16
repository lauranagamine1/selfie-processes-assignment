# Hw 2: Procesos

## Cómo correr el grader

```bash
./grader/self.py processes
```

## Cambios realizados en `selfie.c`

### 1. exit_code declaración de variable
Se asignó `exit_code=0;`.

### 2. Nueva variable global `number_of_processes`
Se agregó `uint64_t number_of_processes = 1;` para controlar cuántas instancias del binario se lanzan concurrentemente.

### 3. Nuevas opciones de línea de comandos `-x` y `-z`
- `-x N` — corre N procesos concurrentes en Mipster
- `-z N` — corre N procesos concurrentes en Hypster

Ambas opciones guardan N en `number_of_processes` y llaman a `selfie_run` con la máquina correspondiente.

### 4. Cambios en `selfie_run()`
- Si `number_of_processes > 1`, la memoria se fija en 64MB por proceso en lugar de usar el argumento del usuario, para evitar conflictos de memoria.
- Se agrega un loop que crea N-1 contextos adicionales después del contexto inicial, ejecutando el boot loader en cada uno.

### 5. Scheduler round-robin en `mipster()`
En cada excepción que no sea EXIT (timer, syscall), el scheduler rota al siguiente contexto con `get_next_context`, volviendo al inicio de `used_contexts` al llegar al final de la lista. En EXIT, se selecciona el sucesor antes de eliminar el contexto con `delete_context`, y la función retorna cuando no quedan más contextos.

### 6. Scheduler round-robin en `hypster()`
Misma lógica que `mipster()` pero usando `hypster_switch`. Además maneja el caso donde `get_parent(from_context) != MY_CONTEXT`, despachando la excepción al contexto padre.