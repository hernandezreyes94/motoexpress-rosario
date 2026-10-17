# MotoExpress Rosario v2 — PWA Firebase

MVP para Villa del Rosario, Zulia. Incluye cliente, registro de motorizado sujeto a aprobación, administración, pedidos compartidos en Firestore, asignación automática al crear pedidos y asignación de pendientes cuando el motorizado pulsa «Buscar pedido» o se conecta. **No está desplegado ni configurado con un proyecto Firebase real.**

## Puesta en marcha

1. Crear un proyecto en https://console.firebase.google.com/ y registrar una aplicación Web.
2. Activar Authentication > Sign-in method > Email/Password y crear Firestore en modo producción.
3. Copiar las credenciales públicas del proyecto en `public/firebase-config.js` (nunca pegar claves privadas de cuenta de servicio).
4. Instalar Node.js 22, ejecutar `npm install -g firebase-tools`, `firebase login` y `firebase use --add` dentro de esta carpeta.
5. Ejecutar `cd functions && npm install && cd ..` y luego `firebase deploy --only firestore,functions,hosting`. **Cloud Functions puede requerir el plan Blaze y facturación habilitada**; revisar cuotas y costos antes de activar.
6. En Firebase Console > Authentication crear la cuenta del administrador, copiar su UID y otorgar el custom claim `admin:true` usando Firebase Admin SDK en un entorno confiable. **Nunca** otorgar el rol desde el frontend. Volver a iniciar sesión para actualizar el token.
7. Los conductores se registran desde la pestaña Motorizado y quedan pendientes hasta la aprobación del administrador.
8. Configurar dominio propio y probar HTTPS, permisos, límites y funcionamiento con dos dispositivos antes de usarlo públicamente.

## Asignación automática

Cuando se crea un pedido, la función `autoAssign` consulta conductores aprobados y conectados. Mediante una transacción reserva el primer conductor disponible que no esté ocupado y asigna el pedido. La selección actual **no es por cercanía ni GPS**, y si no hay conductor disponible el pedido queda pendiente. Los motorizados conectados pueden solicitar pendientes; una versión de producción deberá incorporar reintentos automáticos, vencimientos, rechazo de pedidos, horarios, zonas de cobertura, límites de carga, confirmación de pago y políticas de cancelación. No prometas tiempos de llegada sin geolocalización.

## Seguridad y límites

Firestore prohíbe escrituras directas de pedidos y conductores; solo las Cloud Functions autorizadas modifican estados y asignaciones. Los administradores requieren custom claims. **Pendiente antes de producción:** App Check, verificación de teléfono, rate limits, auditoría, reglas más estrictas para perfil, notificaciones push, manejo de fraude, políticas de privacidad, control de permisos y revisión de requisitos legales y seguros en Venezuela. Los precios son USD de referencia (0–2 km $1.50, 2–5 km $2.50, 5–10 km $4); la distancia es ingresada por el usuario y debe validarse.

## Presupuesto

Meta $500–$1,500 USD para un MVP básico, no cotización. Alojamiento, funciones, mensajería, dominio y soporte pueden tener costos recurrentes. No incluye pasarela de pagos, rastreo GPS en vivo ni apps nativas.
