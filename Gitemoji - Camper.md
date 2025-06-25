### Gitemoji

**Gitemoji** es una convención que utiliza emojis para representar de forma visual el propósito o el tipo de cambio realizado en un commit dentro de un sistema de control de versiones como Git. Es una forma creativa y efectiva de mejorar la legibilidad y comprensión del historial de commits.

### **Cómo funciona Gitemoji**

Cada emoji representa un tipo específico de cambio. Los emojis se colocan al inicio del mensaje de commit, seguidos de una breve descripción del cambio.

Ejemplo

```less
✨ feat: add user authentication module
🐛 fix: resolve null pointer exception in login form
📝 docs: update README with installation instructions
```

### **Emojis comunes**

| Emoji                            | Propósito                           | Tipo sugerido (Conventional Commit) |
| -------------------------------- | ----------------------------------- | ----------------------------------- |
| ✨ (`:sparkles:`)                 | Añadir una nueva funcionalidad      | `feat`                              |
| 🐛 (`:bug:`)                      | Corrección de errores               | `fix`                               |
| 📝 (`:memo:`)                     | Actualizar documentación            | `docs`                              |
| 🎨 (`:art:`)                      | Mejoras de formato o estilo         | `style`                             |
| 🔥 (`:fire:`)                     | Eliminar código o archivos          | `refactor`                          |
| 🚑️ (`:ambulance:`)                | Hotfix (corrección urgente)         | `fix`                               |
| ♻️ (`:recycle:`)                  | Refactorización de código           | `refactor`                          |
| ✅ (`:white_check_mark:`)         | Añadir o modificar pruebas          | `test`                              |
| 🔧 (`:wrench:`)                   | Cambios en configuración            | `chore`                             |
| 🚀 (`:rocket:`)                   | Implementar algo en producción      | `chore`                             |
| ⚡ (`:zap:`)                      | Mejoras de rendimiento              | `perf`                              |
| 🐳 (`:whale:`)                    | Cambios relacionados con Docker     | `chore`                             |
| 📦️ (`:package:`)                  | Añadir o actualizar dependencias    | `chore`                             |
| 💄 (`:lipstick:`)                 | Cambios en el diseño o UI           | `style`                             |
| 🚨 (`:rotating_light:`)           | Solución de advertencias de linting | `fix`                               |
| 🔒 (`:lock:`)                     | Solución de problemas de seguridad  | `fix`                               |
| 🚧 (`:construction:`)             | Trabajo en progreso (WIP)           | `chore`                             |
| 🗑️ (`:wastebasket:`)              | Borrar archivos o código            | `chore`                             |
| 🌱 (`:seedling:`)                 | Inicialización de proyecto          | `chore`                             |
| 📈 (`:chart_with_upwards_trend:`) | Mejorar análisis o seguimiento      | `feat`                              |
| 🐿️ (`:chipmunk:`)                 | Cambios experimentales              | `feat`                              |