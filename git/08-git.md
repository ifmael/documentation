# Git

## git log

EL comando más completo para explorar el historial es `git log`.

Algunas de las opciones más interesantes son:

* `--path` : Se pueden ver exactamente que cambios se han introducido en el commit (en sus líneas).
* Para filtar mensajes que tenga en el mensaje de commit un texto se podría realizar por medio de la opción `--grep`.  Así por ejemplo si se quiere filtar los mensajes que contengan el texsto plátano, se haría: `git log --grep plátano`.
* Para ver todos los commit que han añadido o eliminado una palabra de cualquiera fichero, se añade la opción `-G`: `git log -Gplatano`.
*  Una alternativa al comando anterior es `git grep`.
* Para mostrar los últimos n commit: `git log -<n> --oneline`. Tambien se puede utilizar los `..` para especificar un rango desde hasta, así con `git log HEAD~5..HEAD^5 --oneline`  se muestra los commit desde el quinto commit antes de HEAD  hasta el commit que es padre HEAD. La salida de este último comando es algo confusa puesto que la salida es inversa a como se específica en el rango. Este comando es útil para comparar 2 ramas, pero al contrario que `git diff` que compara los ficheros en las ramas, con el comando anterior se compara la historia. Por ejemplo con `git log <branch-1>..<branch-2> --oneline`, se mostrarían todos los commit que hay en `<branch-2>` pero no en `<branch-1>`.
