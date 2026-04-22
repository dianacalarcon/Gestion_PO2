# Documento de Requisitos — Módulo de Creación de Ofertas 3X Movilidad

## Introducción

El Módulo de Creación de Ofertas es el motor paramétrico central de la estrategia 3X Movilidad de Seguros Bolívar. Su objetivo es habilitar al equipo de negocio para crear, configurar y gestionar ofertas de seguros vehiculares de forma autónoma, sin dependencia de TI, bajo una arquitectura tipo "Lego" donde cada oferta se compone de ingredientes parametrizables: coberturas, exclusiones, asistencias, documentos impresos, variables abiertas y reglas de negocio externas.

### Contexto de Negocio

- **Objetivo estratégico:** Triplicar (3X) la oferta comercial en el segmento de movilidad mediante un motor flexible que permita lanzar productos rápidamente.
- **MVP inicial:** Tres ofertas para el segmento Pesados — Premium, Estándar y Clásico.
- **Usuarios del módulo:** Equipo de gestión y administración (no requiere doble aprobación).
- **Principio arquitectónico:** Separar reglas de negocio del core (asegurabilidad, tarificación, fraude) para que el negocio parametrice sin intervención técnica.
- **Historial de cambios:** El sistema registra historial de cambios (no versionamiento) para trazabilidad completa.

### Tabla de Funcionalidades e Impacto en Crecimiento 3X

| Funcionalidad | Descripción Técnica | Impacto en Crecimiento 3X |
|---|---|---|
| Creación paramétrica de ofertas | Motor tipo Lego con campos dinámicos, listas de valores autónomas y reglas configurables | Reduce time-to-market de semanas a horas para nuevos productos |
| Coberturas con tipos de datos flexibles | Límites, deducibles y valores expresados en %, SMDLV, $COP, días, con reglas condicionales | Permite diferenciación granular entre ofertas (Premium vs Clásico) |
| Duplicación de ofertas | Clonar oferta existente con todos sus componentes para crear variantes | Acelera creación de nuevas ofertas basadas en plantillas exitosas |
| Tipificación coberturas vs servicios | Separación visual y lógica entre coberturas tradicionales y asistencias/servicios | Claridad comercial para intermediarios, mejor experiencia de venta |
| Reglas dinámicas de visibilidad | Intermediario ve solo ofertas aplicables al vehículo consultado | Reduce errores de cotización, mejora conversión |
| Integración con motor tarifario | Conexión API con motor de tarificación para cálculo automático de primas | Cotización en tiempo real sin intervención manual |
| Historial de cambios con auditoría | Registro completo de modificaciones sin versionamiento | Cumplimiento regulatorio y trazabilidad operativa |
| Listas de valores autónomas | Gestión dinámica de catálogos (marcas, líneas, combustibles, canales) | Flexibilidad para adaptarse a cambios de mercado sin deploys |
| Generación de documentos | Carátula, póliza y clausulado parametrizados por oferta | Automatización documental que escala con el volumen de ofertas |
| Códigos únicos auto-generados | Identificador consecutivo único por oferta y cobertura mapeado en todos los procesos | Trazabilidad end-to-end desde cotización hasta siniestro |

## Glosario

- **Motor_Ofertas**: Módulo de software que permite la creación, edición, duplicación, búsqueda e inactivación de ofertas de seguros vehiculares de forma paramétrica.
- **Oferta**: Configuración comercial compuesta por coberturas, exclusiones, asistencias, documentos impresos, variables abiertas y reglas de negocio, dirigida a un segmento vehicular específico.
- **Cobertura**: Componente de una oferta que define la protección otorgada al asegurado, con límites, deducibles y valores configurables en múltiples tipos de datos.
- **Servicio_Asistencia**: Subtipo de cobertura que representa asistencias y servicios (grúa, asistencia jurídica, avería, accidente), diferenciado visualmente pero gestionado bajo la misma estructura técnica.
- **Límite**: Valor máximo de indemnización de una cobertura, expresable en números absolutos, porcentaje (%), SMDLV, pesos colombianos ($COP) o mediante reglas condicionales.
- **Deducible**: Valor que asume el asegurado antes de la indemnización, expresable en números, porcentaje (%), SMDLV, $COP, días o fórmulas compuestas (ej. "10% - MIN 3 SMMLV").
- **SMDLV**: Salario Mínimo Diario Legal Vigente de Colombia, unidad de referencia para expresar valores de coberturas.
- **SMMLV**: Salario Mínimo Mensual Legal Vigente de Colombia, unidad de referencia para expresar deducibles mínimos.
- **Valor_Asegurado**: Valor comercial del vehículo asegurado, utilizado como base para cálculos de coberturas y deducibles.
- **Regla_Condicional**: Lógica de negocio que determina el valor de un límite o deducible en función de condiciones (ej. por rango de Valor_Asegurado).
- **Lista_Valores**: Catálogo dinámico de opciones parametrizables (marcas, líneas, tipos de vehículo, canales, combustibles) gestionado de forma autónoma por el equipo de negocio.
- **Código_Oferta**: Identificador único consecutivo auto-generado que identifica una oferta en todos los procesos del sistema.
- **Código_Cobertura**: Identificador único auto-generado para cada cobertura, mapeado transversalmente en cotización, emisión y siniestros.
- **Intermediario**: Canal comercial (corredor, agente) que accede a las ofertas para cotizar y emitir pólizas.
- **Motor_Tarifario**: Sistema externo de tarificación que calcula primas basándose en la configuración de la oferta y las características del riesgo.
- **Fasecolda**: Guía de referencia de valores comerciales de vehículos en Colombia, consultada desde el sistema core (Tronador).
- **Tronador**: Sistema core de seguros que mantiene la guía Fasecolda y funcionalidades legacy.
- **Hecopot**: Sistema externo de integración para validaciones y consultas complementarias.
- **Historial_Cambios**: Registro cronológico de todas las modificaciones realizadas sobre una oferta, incluyendo usuario, fecha, campo modificado, valor anterior y valor nuevo.
- **Usuario_Administrador**: Línea de negocio de movilidad, autorizada para crear, editar y gestionar ofertas en el Motor_Ofertas.
- **Campos_Adicionales**: Sección extensible de la oferta para captura de datos no contemplados en los campos estándar.
- **Reaseguro**: Configuración de cesión de riesgo a reaseguradoras asociada a una oferta.
- **Coaseguro**: Configuración de distribución de riesgo entre aseguradoras asociada a una oferta.

## Requisitos


### Requisito 1: Creación de Ofertas con Campos Parametrizables

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero crear una nueva oferta de seguros vehiculares configurando todos sus campos de forma paramétrica, para que pueda lanzar productos al mercado sin depender del equipo de TI.

#### Criterios de Aceptación

- **AC1: Formulario de creación completo**
  - **Dado** que el Usuario_Administrador accede al módulo de creación de ofertas
  - **Cuando** abre el formulario de nueva oferta
  - **Entonces** el sistema presenta los campos configurables: Nombre, Descripción, Tipo de Oferta, Dirigido a, Canal Comercial, Clave Intermediario, Tipo de Póliza, Tipo de Colectiva, Tipo de Vehículo Admitido (multi-selección), Tipo de Servicio, Marca (multi-selección con autocompletado), Línea (multi-selección con autocompletado), Tipo de Combustible, Vigencia de Cotizaciones, Requiere Inspección (booleano), Sistema Origen, Forma de Pago, Periodicidad Vigencia, Fecha Inicio Vigencia, Fecha Fin Vigencia, Estado, Color, Qué Transporta, Campos Adicionales, Reaseguro, Coaseguro.

- **AC2: Generación de código y estado inicial**
  - **Dado** que el Usuario_Administrador ha completado todos los campos obligatorios del formulario
  - **Cuando** confirma la creación de la oferta
  - **Entonces** el sistema genera un Código_Oferta único consecutivo y persiste la oferta en estado "Borrador".

- **AC3: Unicidad del nombre de oferta**
  - **Dado** que el sistema está operativo y existen ofertas registradas
  - **Cuando** el Usuario_Administrador intenta crear una oferta con un nombre ya existente
  - **Entonces** el sistema bloquea la creación e informa que el Nombre de la oferta debe ser único.

- **AC4: Carga dinámica de listas de valores**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede a cualquier campo de selección en el formulario de oferta
  - **Entonces** el sistema carga las opciones desde la Lista_Valores correspondiente de forma dinámica.

- **AC5: Filtrado de marca y línea por tipo de vehículo**
  - **Dado** que el Usuario_Administrador está creando una oferta
  - **Cuando** selecciona un Tipo de Vehículo Admitido
  - **Entonces** el sistema filtra las opciones de Marca y Línea para mostrar solo las compatibles con el tipo de vehículo seleccionado.

- **AC6: Validación de campos obligatorios**
  - **Dado** que el Usuario_Administrador tiene campos obligatorios sin completar en el formulario
  - **Cuando** intenta confirmar la creación de la oferta
  - **Entonces** el sistema resalta los campos faltantes con mensajes de validación descriptivos y bloquea la creación.

- **AC7: Registro en historial de cambios**
  - **Dado** que el sistema está operativo
  - **Cuando** se crea exitosamente una nueva oferta
  - **Entonces** el sistema registra la creación en el Historial_Cambios con el usuario, fecha, hora y todos los valores iniciales configurados.

- **AC8: Rendimiento de creación**
  - **Dado** que el Usuario_Administrador ha confirmado la creación de una oferta
  - **Cuando** el sistema procesa la operación
  - **Entonces** la operación de creación se completa en menos de 3 segundos.

---

### Requisito 2: Gestión de Coberturas por Oferta

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero asociar N coberturas a una oferta configurando límites, deducibles y valores en múltiples tipos de datos, para que pueda definir la protección exacta que ofrece cada producto.

#### Criterios de Aceptación

- **AC1: Gestión ilimitada de coberturas**
  - **Dado** que el Usuario_Administrador accede a la sección de coberturas de una oferta
  - **Cuando** interactúa con la lista de coberturas
  - **Entonces** el sistema permite agregar, editar y eliminar coberturas sin límite de cantidad.

- **AC2: Campos de configuración por cobertura**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador agrega o edita una cobertura
  - **Entonces** el sistema presenta los campos: Nombre, Descripción, Límite (con selector de tipo de dato), Valor de Cobertura, Deducible (con selector de tipo de dato), Estado (activa/inactiva), Obligatoria/Opcional (booleano), Fecha Inicio Vigencia, Fecha Fin Vigencia.

- **AC3: Tipos de dato para límite**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura el campo Límite de una cobertura
  - **Entonces** el sistema permite expresarlo en: número absoluto, porcentaje (%) del Valor_Asegurado, número en SMDLV, pesos colombianos ($COP), o mediante una Regla_Condicional.

- **AC4: Tipos de dato para deducible**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura el campo Deducible de una cobertura
  - **Entonces** el sistema permite expresarlo en: número absoluto, porcentaje (%), número en SMDLV, pesos colombianos ($COP), días, o fórmulas compuestas (ej. "10% - MIN 3 SMMLV").

- **AC5: Valor de cobertura en días o N/A**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura el campo Valor de Cobertura
  - **Entonces** el sistema permite expresarlo en días o N/A.

- **AC6: Configuración de reglas condicionales**
  - **Dado** que el Usuario_Administrador está configurando el límite de una cobertura
  - **Cuando** selecciona el tipo Regla_Condicional
  - **Entonces** el sistema permite definir rangos de condición con sus valores asociados (ej. Valor_Asegurado menor o igual a 150 millones: 8 SMDLV diarios; Valor_Asegurado mayor a 150 millones: 12 SMDLV diarios).

- **AC7: Generación de código de cobertura**
  - **Dado** que el Usuario_Administrador ha configurado una cobertura
  - **Cuando** guarda la cobertura
  - **Entonces** el sistema genera un Código_Cobertura único auto-generado que se mapea transversalmente en los procesos de cotización, emisión y siniestros.

- **AC8: Marcado obligatoria u opcional**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura una cobertura dentro de una oferta
  - **Entonces** el sistema permite marcar cada cobertura como obligatoria u opcional.

- **AC9: Confirmación al eliminar cobertura obligatoria**
  - **Dado** que existe una cobertura marcada como obligatoria en la oferta
  - **Cuando** el Usuario_Administrador intenta eliminarla
  - **Entonces** el sistema solicita confirmación explícita antes de proceder.

- **AC10: Unicidad global del código de cobertura**
  - **Dado** que existen coberturas creadas en el sistema
  - **Cuando** se crea cualquier nueva cobertura
  - **Entonces** el sistema garantiza que el Código_Cobertura sea único a nivel global del sistema.

---

### Requisito 3: Gestión de Servicios y Asistencias

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero gestionar asistencias y servicios como un subtipo diferenciado de cobertura dentro de la oferta, para que los intermediarios distingan claramente entre coberturas tradicionales y servicios de asistencia.

#### Criterios de Aceptación

- **AC1: Sección separada de servicios y asistencias**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede a la configuración de una oferta
  - **Entonces** el sistema presenta una sección separada denominada "Servicios y Asistencias", visualmente diferenciada de la sección de coberturas tradicionales.

- **AC2: Misma estructura técnica que coberturas**
  - **Dado** que el sistema está operativo
  - **Cuando** se gestiona un Servicio_Asistencia
  - **Entonces** el sistema lo gestiona bajo la misma estructura técnica de datos que las coberturas (Nombre, Descripción, Límite, Deducible, Valor, Estado, Obligatoria/Opcional, Vigencia).

- **AC3: Asignación automática de tipo servicio**
  - **Dado** que el Usuario_Administrador está en la sección de Servicios y Asistencias
  - **Cuando** crea un nuevo Servicio_Asistencia
  - **Entonces** el sistema asigna automáticamente el tipo "Servicio" para diferenciarlo de las coberturas tradicionales de tipo "Cobertura".

- **AC4: Tipos de evento para servicios**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura un servicio
  - **Entonces** el sistema permite configurar tipos de evento específicos: Avería y Accidente, cada uno con límites y condiciones independientes.

- **AC5: Presentación separada para intermediarios**
  - **Dado** que el Intermediario consulta una oferta disponible
  - **Cuando** visualiza el detalle de la oferta
  - **Entonces** el sistema presenta las coberturas tradicionales y los Servicio_Asistencia en secciones separadas con etiquetas claras.

- **AC6: Código único y mapeo transversal para servicios**
  - **Dado** que existen Servicio_Asistencia en el sistema
  - **Cuando** se crea cualquier nuevo Servicio_Asistencia
  - **Entonces** el sistema aplica las mismas reglas de Código_Cobertura único y mapeo transversal que a las coberturas.

---

### Requisito 4: Edición de Ofertas con Historial de Cambios

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero editar cualquier campo de una oferta existente y que el sistema registre automáticamente cada cambio realizado, para que exista trazabilidad completa sin necesidad de crear versiones.

#### Criterios de Aceptación

- **AC1: Edición completa de oferta**
  - **Dado** que el Usuario_Administrador accede a una oferta existente en modo edición
  - **Cuando** modifica cualquier campo de la oferta, sus coberturas o sus Servicio_Asistencia
  - **Entonces** el sistema permite la modificación de todos los campos.

- **AC2: Registro detallado de cambios**
  - **Dado** que el Usuario_Administrador ha realizado modificaciones en una oferta
  - **Cuando** guarda los cambios
  - **Entonces** el sistema registra en el Historial_Cambios: usuario que realizó el cambio, fecha y hora exacta (UTC), campo modificado, valor anterior y valor nuevo.

- **AC3: Registro único sin versionamiento**
  - **Dado** que el sistema está operativo
  - **Cuando** se edita cualquier oferta
  - **Entonces** el sistema mantiene un único registro de oferta actualizado (no crea versiones separadas) y preserva el historial completo de modificaciones.

- **AC4: Consulta del historial de cambios**
  - **Dado** que el Usuario_Administrador accede al Historial_Cambios de una oferta
  - **Cuando** consulta los cambios registrados
  - **Entonces** el sistema presenta los cambios en orden cronológico descendente (más reciente primero) con filtros por fecha, usuario y campo modificado.

- **AC5: Historial de cambios en componentes asociados**
  - **Dado** que el sistema está operativo
  - **Cuando** se modifican coberturas o Servicio_Asistencia asociados a una oferta
  - **Entonces** el sistema registra los cambios dentro del mismo Historial_Cambios, identificando claramente el componente modificado.

- **AC6: Inactivación automática por fecha expirada**
  - **Dado** que existe una oferta activa en el sistema
  - **Cuando** el Usuario_Administrador modifica la Fecha Fin Vigencia a una fecha pasada
  - **Entonces** el sistema cambia automáticamente el estado a "Inactiva" y registra ambos cambios en el Historial_Cambios.

- **AC7: Consistencia del valor anterior en historial**
  - **Dado** que se registran modificaciones en el Historial_Cambios
  - **Cuando** se almacena cualquier cambio
  - **Entonces** el sistema garantiza que el valor anterior almacenado corresponda exactamente al estado del campo antes de la modificación.

---

### Requisito 5: Duplicación de Ofertas

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero duplicar una oferta existente con todas sus coberturas, servicios y configuraciones, para que pueda crear variantes de productos rápidamente sin reconfigurar desde cero.

#### Criterios de Aceptación

- **AC1: Copia completa de la oferta**
  - **Dado** que existe una oferta en el sistema
  - **Cuando** el Usuario_Administrador selecciona la acción "Duplicar" sobre esa oferta
  - **Entonces** el sistema crea una copia completa incluyendo: todos los campos de configuración, todas las coberturas con sus límites, deducibles y reglas condicionales, todos los Servicio_Asistencia, exclusiones, documentos impresos y campos adicionales.

- **AC2: Nuevo código de oferta para la copia**
  - **Dado** que el sistema está operativo
  - **Cuando** se duplica una oferta
  - **Entonces** el sistema asigna un nuevo Código_Oferta único consecutivo a la oferta duplicada.

- **AC3: Nuevos códigos de cobertura para la copia**
  - **Dado** que el sistema está operativo
  - **Cuando** se duplica una oferta
  - **Entonces** el sistema asigna nuevos Código_Cobertura únicos a cada cobertura y Servicio_Asistencia de la oferta duplicada.

- **AC4: Nombre y estado de la copia**
  - **Dado** que el sistema está operativo
  - **Cuando** se duplica una oferta
  - **Entonces** el sistema establece el Nombre como "[Nombre original] - Copia" y el estado como "Borrador".

- **AC5: Redirección a edición tras duplicación**
  - **Dado** que la oferta duplicada se ha creado exitosamente
  - **Cuando** el sistema completa la operación de duplicación
  - **Entonces** redirige al Usuario_Administrador al formulario de edición de la nueva oferta para que pueda personalizar los campos.

- **AC6: Registro de duplicación en historial**
  - **Dado** que el sistema está operativo
  - **Cuando** se duplica una oferta
  - **Entonces** el sistema registra la duplicación en el Historial_Cambios de la nueva oferta indicando que fue creada como copia de la oferta original (referenciando el Código_Oferta de origen).

- **AC7: Independencia de la oferta duplicada**
  - **Dado** que existe una oferta duplicada en el sistema
  - **Cuando** se modifica la copia o la original
  - **Entonces** el sistema garantiza que la oferta duplicada sea completamente independiente de la original (modificar la copia no afecta la original y viceversa).

---

### Requisito 6: Inactivación de Ofertas

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero inactivar una oferta que ya no debe estar disponible para cotización, para que los intermediarios no puedan generar nuevas pólizas con productos descontinuados.

#### Criterios de Aceptación

- **AC1: Confirmación antes de inactivar**
  - **Dado** que existe una oferta activa en el sistema
  - **Cuando** el Usuario_Administrador selecciona la acción "Inactivar"
  - **Entonces** el sistema solicita confirmación explícita antes de proceder.

- **AC2: Cambio de estado y registro en historial**
  - **Dado** que el Usuario_Administrador ha solicitado inactivar una oferta
  - **Cuando** confirma la inactivación
  - **Entonces** el sistema cambia el estado de la oferta a "Inactiva" y registra el cambio en el Historial_Cambios.

- **AC3: Exclusión de oferta inactiva para intermediarios**
  - **Dado** que una oferta se encuentra en estado "Inactiva"
  - **Cuando** el Intermediario busca ofertas disponibles o accede al módulo de cotización
  - **Entonces** el sistema excluye la oferta de los resultados.

- **AC4: Visibilidad para administrador con indicador**
  - **Dado** que una oferta se encuentra en estado "Inactiva"
  - **Cuando** el Usuario_Administrador accede a la vista de gestión de ofertas
  - **Entonces** el sistema mantiene la oferta visible con un indicador visual de estado inactivo.

- **AC5: Reactivación de oferta con vigencia válida**
  - **Dado** que existe una oferta inactiva cuya Fecha Fin Vigencia no ha expirado
  - **Cuando** el Usuario_Administrador solicita reactivarla
  - **Entonces** el sistema permite cambiar su estado a "Activa".

- **AC6: Bloqueo de reactivación con vigencia expirada**
  - **Dado** que existe una oferta inactiva cuya Fecha Fin Vigencia ya expiró
  - **Cuando** el Usuario_Administrador intenta reactivarla
  - **Entonces** el sistema bloquea la reactivación y muestra un mensaje indicando que debe actualizar la fecha de vigencia primero.

---

### Requisito 7: Búsqueda y Filtrado de Ofertas

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero buscar y filtrar ofertas por múltiples criterios, para que pueda localizar rápidamente la oferta que necesito gestionar entre un catálogo creciente de productos.

#### Criterios de Aceptación

- **AC1: Panel de búsqueda por texto libre**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede al módulo de gestión de ofertas
  - **Entonces** el sistema presenta un panel de búsqueda con campo de texto libre que busca coincidencias en Nombre, Descripción y Código_Oferta.

- **AC2: Filtros combinables**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede al panel de búsqueda
  - **Entonces** el sistema presenta filtros combinables por: Estado (Activa, Inactiva, Borrador), Tipo de Oferta, Canal Comercial, Tipo de Vehículo Admitido, Rango de Fecha de Vigencia y Sistema Origen.

- **AC3: Combinación de filtros con operación AND**
  - **Dado** que el Usuario_Administrador ha seleccionado múltiples filtros
  - **Cuando** aplica la búsqueda
  - **Entonces** el sistema combina los criterios con operación AND y muestra solo las ofertas que cumplan todos los filtros.

- **AC4: Información mostrada en resultados**
  - **Dado** que existen ofertas que coinciden con los criterios de búsqueda
  - **Cuando** el sistema presenta los resultados
  - **Entonces** muestra para cada oferta: Código_Oferta, Nombre, Estado, Tipo de Oferta, Canal Comercial, Fecha Inicio Vigencia, Fecha Fin Vigencia y cantidad de coberturas asociadas.

- **AC5: Rendimiento de búsqueda**
  - **Dado** que el Usuario_Administrador ejecuta una búsqueda
  - **Cuando** el sistema procesa la consulta
  - **Entonces** la búsqueda se ejecuta con una latencia percibida inferior a 500 milisegundos.

- **AC6: Mensaje cuando no hay resultados**
  - **Dado** que el Usuario_Administrador ha aplicado filtros de búsqueda
  - **Cuando** no existen ofertas que coincidan con los criterios
  - **Entonces** el sistema muestra un mensaje indicando "No se encontraron ofertas con los criterios seleccionados" y sugiere limpiar los filtros.

- **AC7: Ordenamiento de resultados**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador visualiza los resultados de búsqueda
  - **Entonces** el sistema permite ordenar los resultados por Nombre, Código_Oferta, Fecha de Creación o Estado.

---

### Requisito 8: Reglas de Visibilidad para Intermediarios

**Historia de Usuario:** Como Intermediario, quiero ver solo las ofertas aplicables al vehículo que estoy consultando, para que pueda cotizar de forma precisa sin riesgo de seleccionar un producto incompatible.

#### Criterios de Aceptación

- **AC1: Filtrado por compatibilidad del vehículo**
  - **Dado** que el Intermediario consulta ofertas disponibles para un vehículo específico
  - **Cuando** el sistema evalúa las ofertas disponibles
  - **Entonces** filtra y muestra solo las ofertas cuyo Tipo de Vehículo Admitido, Marca, Línea, Tipo de Servicio y Tipo de Combustible sean compatibles con las características del vehículo consultado.

- **AC2: Exclusión de ofertas inactivas y borrador**
  - **Dado** que existen ofertas en estado "Inactiva" o "Borrador"
  - **Cuando** el Intermediario consulta ofertas disponibles
  - **Entonces** el sistema excluye todas las ofertas en esos estados de la vista del Intermediario.

- **AC3: Exclusión de ofertas con vigencia expirada**
  - **Dado** que existen ofertas cuya Fecha Fin Vigencia ha expirado
  - **Cuando** el Intermediario consulta ofertas disponibles
  - **Entonces** el sistema excluye todas las ofertas expiradas de la vista del Intermediario.

- **AC4: Filtrado por canal comercial**
  - **Dado** que el Intermediario pertenece a un canal comercial específico
  - **Cuando** consulta ofertas disponibles
  - **Entonces** el sistema muestra solo las ofertas configuradas para ese Canal Comercial o para "Todos los canales".

- **AC5: Presentación de coberturas obligatorias y opcionales**
  - **Dado** que el Intermediario visualiza una oferta aplicable
  - **Cuando** revisa el detalle de coberturas
  - **Entonces** el sistema presenta las coberturas obligatorias como incluidas por defecto y las coberturas opcionales como seleccionables.

- **AC6: Mensaje cuando no hay ofertas aplicables**
  - **Dado** que el Intermediario consulta ofertas para un vehículo específico
  - **Cuando** no existen ofertas aplicables para las características del vehículo
  - **Entonces** el sistema muestra un mensaje indicando que no hay ofertas disponibles para ese vehículo.

- **AC7: Rendimiento de resolución de visibilidad**
  - **Dado** que el Intermediario consulta ofertas disponibles
  - **Cuando** el sistema resuelve la visibilidad
  - **Entonces** la resolución se completa en menos de 2 segundos.

---

### Requisito 9: Gestión de Listas de Valores Dinámicas

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero gestionar de forma autónoma los catálogos de valores (marcas, líneas, tipos de vehículo, canales, combustibles), para que pueda adaptar las opciones disponibles sin requerir cambios en el código o deploys.

#### Criterios de Aceptación

- **AC1: Interfaz de administración de catálogos**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede a la administración de Lista_Valores
  - **Entonces** el sistema proporciona una interfaz para gestionar los catálogos: Tipo de Oferta, Canal Comercial, Tipo de Póliza, Tipo de Colectiva, Tipo de Vehículo, Tipo de Servicio, Marca, Línea, Tipo de Combustible, Forma de Pago, Periodicidad Vigencia, Sistema Origen y Qué Transporta.

- **AC2: Disponibilidad inmediata de nuevos valores**
  - **Dado** que el Usuario_Administrador está gestionando una Lista_Valores
  - **Cuando** agrega un nuevo valor al catálogo
  - **Entonces** el sistema hace disponible el nuevo valor inmediatamente en todos los formularios que consuman esa lista.

- **AC3: Activación e inactivación sin eliminación**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador gestiona valores de una Lista_Valores
  - **Entonces** el sistema permite activar e inactivar valores individuales sin eliminarlos, preservando la integridad referencial con ofertas existentes.

- **AC4: Aviso al inactivar valor en uso**
  - **Dado** que un valor de Lista_Valores está siendo utilizado por ofertas activas
  - **Cuando** el Usuario_Administrador intenta inactivarlo
  - **Entonces** el sistema muestra un aviso indicando la cantidad de ofertas afectadas y solicita confirmación.

- **AC5: Relaciones jerárquicas entre listas**
  - **Dado** que el sistema está operativo
  - **Cuando** se gestionan listas de valores con dependencias
  - **Entonces** el sistema soporta relaciones jerárquicas entre listas (ej. Línea depende de Marca, Marca depende de Tipo de Vehículo).

- **AC6: Auditoría de cambios en listas de valores**
  - **Dado** que el sistema está operativo
  - **Cuando** se realiza cualquier cambio en una Lista_Valores
  - **Entonces** el sistema registra el cambio en un log de auditoría con usuario, fecha y acción realizada.


---

### Requisito 10: Integración con Motor Tarifario y Sistemas Externos

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero que las ofertas se integren con el motor tarifario y los sistemas externos (Fasecolda, Hecopot), para que las cotizaciones se calculen automáticamente y los datos de referencia estén siempre actualizados.

#### Criterios de Aceptación

- **AC1: Envío de configuración al motor tarifario**
  - **Dado** que una oferta en estado "Activa" es seleccionada para cotización
  - **Cuando** el sistema inicia el proceso de tarificación
  - **Entonces** envía la configuración completa de coberturas, límites, deducibles y reglas condicionales al Motor_Tarifario vía API REST versionada.

- **AC2: Recepción y presentación de primas**
  - **Dado** que el sistema está operativo
  - **Cuando** el Motor_Tarifario responde con el cálculo de primas
  - **Entonces** el sistema recibe el cálculo de prima por cobertura y prima total, y presenta los resultados al Intermediario en menos de 5 segundos.

- **AC3: Manejo de timeout del motor tarifario**
  - **Dado** que se ha enviado una solicitud al Motor_Tarifario
  - **Cuando** el Motor_Tarifario no responde en 15 segundos
  - **Entonces** el sistema retorna un error descriptivo al usuario, activa el circuit breaker y registra el evento en el sistema de logs centralizado con Correlation-ID.

- **AC4: Consulta de guía Fasecolda**
  - **Dado** que el sistema está operativo
  - **Cuando** se requieren valores comerciales de referencia de vehículos
  - **Entonces** el sistema consulta la guía Fasecolda desde Tronador utilizando la integración existente del sistema core.

- **AC5: Integración con Hecopot**
  - **Dado** que el sistema está operativo
  - **Cuando** se requieren validaciones complementarias durante cotización y emisión
  - **Entonces** el sistema se integra con Hecopot para realizar dichas validaciones.

- **AC6: Retries con backoff exponencial**
  - **Dado** que el sistema está operativo
  - **Cuando** una llamada a un sistema externo falla (Motor_Tarifario, Tronador, Hecopot)
  - **Entonces** el sistema implementa retries con backoff exponencial.

- **AC7: Propagación de Correlation-ID**
  - **Dado** que el sistema está operativo
  - **Cuando** se realiza cualquier petición a sistemas externos
  - **Entonces** el sistema propaga un Correlation-ID único para trazabilidad end-to-end.

- **AC8: Versionamiento de APIs**
  - **Dado** que el sistema está operativo
  - **Cuando** se exponen o consumen APIs de integración
  - **Entonces** el sistema versiona todas las APIs con prefijo en ruta (ej. /v1/api/ofertas) conforme al estándar API-First de la organización.

---

### Requisito 11: Gestión de Exclusiones y Restricciones

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero configurar exclusiones, clientes restringidos y marcas restringidas por oferta, para que pueda controlar con precisión el alcance de cada producto y mitigar riesgos.

#### Criterios de Aceptación

- **AC1: Asociación de exclusiones textuales**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura exclusiones para una oferta
  - **Entonces** el sistema permite asociar N exclusiones textuales, cada una con Nombre, Descripción y Estado (activa/inactiva).

- **AC2: Lista de clientes restringidos**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura restricciones para una oferta
  - **Entonces** el sistema permite configurar una lista de clientes restringidos, impidiendo que dichos clientes accedan a la cotización de esa oferta.

- **AC3: Lista de marcas restringidas**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura restricciones para una oferta
  - **Entonces** el sistema permite configurar una lista de marcas restringidas, excluyendo vehículos de esas marcas de la elegibilidad.

- **AC4: Bloqueo de cotización para restringidos**
  - **Dado** que existe un cliente o marca restringida para una oferta
  - **Cuando** el Intermediario intenta cotizar esa oferta para el cliente o marca restringida
  - **Entonces** el sistema bloquea la cotización y muestra un mensaje indicando que el cliente o vehículo no es elegible.

- **AC5: Gestión en el nuevo sistema**
  - **Dado** que el sistema está operativo
  - **Cuando** se gestionan listas de clientes y marcas restringidas
  - **Entonces** el sistema las gestiona dentro del nuevo sistema (no en el core Tronador).

- **AC6: Registro de cambios en exclusiones y restricciones**
  - **Dado** que el sistema está operativo
  - **Cuando** se realizan cambios en exclusiones o restricciones de una oferta
  - **Entonces** el sistema registra todos los cambios en el Historial_Cambios de la oferta.

---

### Requisito 12: Generación de Documentos por Oferta

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero configurar los documentos impresos (carátula, póliza, clausulado) asociados a cada oferta, para que la emisión de pólizas genere automáticamente la documentación correcta según el producto.

#### Criterios de Aceptación

- **AC1: Tipos de documento asociables**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador configura documentos para una oferta
  - **Entonces** el sistema permite asociar los siguientes tipos de documento impreso: Carátula, Póliza y Clausulado.

- **AC2: Selección de plantilla y variables dinámicas**
  - **Dado** que el Usuario_Administrador está configurando un documento para una oferta
  - **Cuando** selecciona el tipo de documento
  - **Entonces** el sistema permite seleccionar una plantilla de documento y asociar las variables dinámicas de la oferta (coberturas, límites, deducibles, datos del asegurado).

- **AC3: Generación de documentos en emisión**
  - **Dado** que el sistema está operativo
  - **Cuando** se emite una póliza basada en una oferta
  - **Entonces** el sistema genera los documentos con los datos parametrizados de la oferta y las coberturas seleccionadas.

- **AC4: Repositorio centralizado de plantillas**
  - **Dado** que el sistema está operativo
  - **Cuando** se accede a las plantillas de documentos
  - **Entonces** el sistema las almacena en un repositorio centralizado accesible por el Motor_Ofertas.

- **AC5: Bloqueo de activación sin documentos**
  - **Dado** que una oferta no tiene documentos configurados
  - **Cuando** el Usuario_Administrador intenta activarla
  - **Entonces** el sistema bloquea la activación y muestra un mensaje indicando que debe configurar al menos la Carátula y el Clausulado.

---

### Requisito 13: Configuración de Reaseguro y Coaseguro

**Historia de Usuario:** Como Línea de negocio de movilidad, quiero configurar los parámetros de reaseguro y coaseguro por oferta, para que la distribución de riesgo quede definida desde la parametrización del producto.

#### Criterios de Aceptación

- **AC1: Sección de configuración de reaseguro**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede a la configuración de una oferta
  - **Entonces** el sistema presenta una sección de Reaseguro con campos para: tipo de reaseguro, porcentaje de cesión, reaseguradora asociada y condiciones especiales.

- **AC2: Sección de configuración de coaseguro**
  - **Dado** que el sistema está operativo
  - **Cuando** el Usuario_Administrador accede a la configuración de una oferta
  - **Entonces** el sistema presenta una sección de Coaseguro con campos para: porcentaje de participación por aseguradora y aseguradora líder.

- **AC3: Validación de porcentajes**
  - **Dado** que el Usuario_Administrador está configurando reaseguro o coaseguro
  - **Cuando** ingresa los porcentajes de cesión o participación
  - **Entonces** el sistema valida que sumen un total coherente (no excedan 100%).

- **AC4: Registro de cambios en reaseguro y coaseguro**
  - **Dado** que el sistema está operativo
  - **Cuando** se realizan cambios en la configuración de reaseguro o coaseguro
  - **Entonces** el sistema registra todos los cambios en el Historial_Cambios de la oferta.

---

### Requisito 14: Seguridad, Rendimiento y Escalabilidad del Motor de Ofertas

**Historia de Usuario:** Como Arquitecto de Solución, quiero que el Motor de Ofertas cumpla con los estándares de seguridad, rendimiento y escalabilidad de la organización, para que soporte picos de demanda masiva consistentes con el crecimiento 3X.

#### Criterios de Aceptación

- **AC1: Autenticación OAuth 2.0 con JWT**
  - **Dado** que el sistema está operativo
  - **Cuando** se accede a cualquier endpoint del Motor_Ofertas
  - **Entonces** el sistema protege todos los endpoints mediante autenticación OAuth 2.0 con tokens JWT firmados por el proveedor de identidad institucional.

- **AC2: Autorización basada en roles (RBAC)**
  - **Dado** que el sistema está operativo
  - **Cuando** un usuario autenticado accede al sistema
  - **Entonces** el sistema implementa autorización basada en roles diferenciando al menos: Usuario_Administrador (CRUD completo), Intermediario (solo lectura de ofertas aplicables) y Auditor (solo lectura de historial).

- **AC3: Rate limiting diferenciado**
  - **Dado** que el sistema está operativo
  - **Cuando** un usuario autenticado realiza peticiones
  - **Entonces** el sistema aplica rate limiting de 200 peticiones por minuto por usuario en endpoints de consulta y 50 peticiones por minuto en endpoints de escritura.

- **AC4: Latencia de consulta**
  - **Dado** que el sistema opera bajo carga normal (hasta 500 peticiones concurrentes)
  - **Cuando** se realizan peticiones de consulta de ofertas
  - **Entonces** el sistema responde con una latencia inferior a 200 milisegundos (p95).

- **AC5: Latencia de escritura**
  - **Dado** que el sistema está operativo
  - **Cuando** se realizan peticiones de escritura (creación, edición, duplicación)
  - **Entonces** el sistema responde con una latencia inferior a 3 segundos (p95).

- **AC6: Caché en memoria**
  - **Dado** que el sistema está operativo
  - **Cuando** se consultan ofertas activas o Lista_Valores frecuentemente
  - **Entonces** el sistema implementa caché en memoria (ElastiCache/Redis) con TTL configurable.

- **AC7: Auto-scaling horizontal**
  - **Dado** que el sistema está operativo
  - **Cuando** se presentan picos de demanda
  - **Entonces** el sistema soporta auto-scaling horizontal para manejar la carga sin degradación de rendimiento.

- **AC8: Correlation-ID en peticiones**
  - **Dado** que el sistema está operativo
  - **Cuando** se procesa cualquier petición
  - **Entonces** el sistema incluye un Correlation-ID único y lo propaga a todos los servicios downstream.

- **AC9: Logs estructurados**
  - **Dado** que el sistema está operativo
  - **Cuando** se genera cualquier evento de log
  - **Entonces** el sistema envía logs estructurados (JSON) al sistema de monitoreo centralizado (CloudWatch) con campos: timestamp, level, service, correlation-id, message, user-id, action.

- **AC10: Circuit breakers para integraciones**
  - **Dado** que el sistema está operativo
  - **Cuando** se realizan llamadas a sistemas externos (Motor_Tarifario, Tronador, Hecopot)
  - **Entonces** el sistema implementa circuit breakers para todas las integraciones.

- **AC11: Enmascaramiento de datos sensibles**
  - **Dado** que el sistema está operativo
  - **Cuando** se generan respuestas de API o logs
  - **Entonces** el sistema enmascara datos sensibles (PII, datos financieros) en todas las respuestas y logs.

- **AC12: Security headers**
  - **Dado** que el sistema está operativo
  - **Cuando** se envía cualquier respuesta HTTP
  - **Entonces** el sistema aplica security headers: Content-Security-Policy, X-Content-Type-Options, X-Frame-Options, Strict-Transport-Security, Referrer-Policy.

- **AC13: Validación y sanitización de inputs**
  - **Dado** que el sistema está operativo
  - **Cuando** se reciben datos de entrada en los boundaries del sistema
  - **Entonces** el sistema valida y sanitiza todos los inputs para prevenir inyecciones SQL, XSS y path traversal.

- **AC14: Bloqueo por intentos fallidos de autenticación**
  - **Dado** que se detectan más de 10 intentos fallidos de autenticación desde una misma IP en 5 minutos
  - **Cuando** se alcanza el umbral de intentos
  - **Entonces** el sistema bloquea temporalmente la IP por 15 minutos y registra una alerta en el sistema de monitoreo.

---

### Requisito 15: Modelo de Datos y Persistencia

**Historia de Usuario:** Como Arquitecto de Solución, quiero que el modelo de datos del Motor de Ofertas sea flexible, extensible y garantice integridad referencial, para que soporte la evolución del catálogo de productos sin migraciones disruptivas.

#### Criterios de Aceptación

- **AC1: Persistencia en PostgreSQL**
  - **Dado** que el sistema está operativo
  - **Cuando** se almacenan datos de ofertas, coberturas, Servicio_Asistencia, exclusiones, restricciones, documentos y configuraciones de reaseguro/coaseguro
  - **Entonces** el sistema los almacena en PostgreSQL 15.4+.

- **AC2: Campos adicionales como JSONB**
  - **Dado** que el sistema está operativo
  - **Cuando** se gestiona el campo Campos_Adicionales de una oferta
  - **Entonces** el sistema lo implementa como tipo de dato JSONB en PostgreSQL para permitir extensibilidad sin cambios de esquema.

- **AC3: Integridad referencial con foreign keys**
  - **Dado** que el sistema está operativo
  - **Cuando** se gestionan relaciones entre ofertas y sus componentes (coberturas, servicios, exclusiones, documentos)
  - **Entonces** el sistema garantiza integridad referencial mediante foreign keys con restricción ON DELETE CASCADE controlada.

- **AC4: Índices optimizados para consultas frecuentes**
  - **Dado** que el sistema está operativo
  - **Cuando** se ejecutan consultas frecuentes
  - **Entonces** el sistema implementa índices optimizados para: búsqueda por Código_Oferta, filtrado por estado, filtrado por tipo de vehículo y búsqueda de texto en nombre/descripción.

- **AC5: Historial de cambios con particionamiento**
  - **Dado** que el sistema está operativo
  - **Cuando** se almacena el Historial_Cambios
  - **Entonces** el sistema lo almacena en una tabla dedicada con particionamiento por fecha para optimizar consultas históricas y gestionar el crecimiento de datos.

- **AC6: Soft delete para ofertas y coberturas**
  - **Dado** que el sistema está operativo
  - **Cuando** se elimina una oferta o cobertura
  - **Entonces** el sistema implementa soft delete (marcar como eliminadas sin borrar físicamente) para preservar integridad referencial con cotizaciones y pólizas existentes.

- **AC7: Reglas condicionales como JSONB**
  - **Dado** que el sistema está operativo
  - **Cuando** se almacenan Regla_Condicional
  - **Entonces** el sistema las almacena como estructuras JSONB que permiten definir condiciones y valores de forma flexible sin cambios de esquema.

- **AC8: Control de concurrencia optimista**
  - **Dado** que múltiples usuarios pueden editar la misma oferta simultáneamente
  - **Cuando** se realizan operaciones de escritura concurrentes sobre la misma oferta
  - **Entonces** el sistema implementa control de concurrencia optimista para prevenir pérdida de datos por escrituras simultáneas.

---

## Notas Técnicas

### Stack Tecnológico

- **Frontend:** Angular 17+ (LTS) con TypeScript 5.x, o React 18.x + Vite 5.x como alternativa aprobada
- **UI:** Componentes accesibles (Shadcn UI / Radix UI), Tailwind CSS 3.x para estilizado
- **Backend:** Node.js 20.x (LTS) con Express.js 4.x o Fastify 4.x
- **Validación:** Zod 3.x para validación de esquemas TypeScript-first
- **Persistencia:** PostgreSQL 15.4+ (AWS RDS) con campos JSONB para extensibilidad
- **Caché:** ElastiCache (Redis) para consultas frecuentes y listas de valores
- **Infraestructura:** AWS ECS Fargate, S3, CloudFront (CDN), ALB
- **CI/CD:** GitHub Actions con stages de build, test, SAST, SCA y deploy progresivo (blue-green)
- **Monitoreo:** AWS CloudWatch para logs estructurados y métricas

### Arquitectura

- Separación estricta de capas: Presentación (Angular/React), Lógica (Node.js API), Persistencia (PostgreSQL + Redis)
- API-First con versionamiento en ruta (`/v1/api/ofertas`, `/v1/api/coberturas`, `/v1/api/listas-valores`)
- Motor de ofertas fuera del core (Tronador) — integración vía APIs REST
- Reglas de negocio parametrizables, no hardcodeadas en código
- Correlation-ID obligatorio en todas las peticiones entre capas
- Logs estructurados (JSON) enviados a CloudWatch centralizado
- Retries con backoff exponencial y Circuit Breakers en todas las llamadas a sistemas externos
- Control de concurrencia optimista para escrituras simultáneas
- Auto-scaling horizontal en ECS Fargate para picos de demanda 3X

### Seguridad

- OAuth 2.0 / OIDC para autenticación; JWT firmados para autorización
- RBAC con roles: Administrador, Intermediario, Auditor
- CORS restringido a dominios autorizados
- Rate limiting diferenciado: lectura (200 req/min) y escritura (50 req/min)
- Tokens en httpOnly cookies (no localStorage)
- Helmet para security headers
- Datos sensibles enmascarados en respuestas de API y logs
- Secret management vía AWS Parameter Store
- Validación y sanitización de todos los inputs en boundaries del sistema
- Comunicación exclusivamente mediante DNS (no IPs fijas)

### Integraciones

| Sistema | Tipo | Propósito |
|---|---|---|
| Motor_Tarifario | API REST | Cálculo de primas por cobertura y prima total |
| Tronador (Core) | API REST | Consulta guía Fasecolda, datos de referencia |
| Hecopot | API REST | Validaciones complementarias |
| Módulo de Cotización | API REST | Consumo de ofertas activas para cotización |
| Sistema de Emisión | API REST | Generación de pólizas con documentos parametrizados |

### Ofertas MVP Iniciales

| Oferta | Tipo Vehículo | Coberturas Principales | Diferenciador |
|---|---|---|---|
| Pesados Premium | Camiones, Furgones, Chásis, Remolques, Camionetas cerradas de reparto | RC Extracontractual (5,250M), GAP Patrimonial (20% VA), Accidentes Personales (30M), Renta Diaria condicional | Máxima protección, bolsa combinada más alta |
| Pesados Estándar | Camiones, Furgones, Chásis, Remolques, Camionetas cerradas de reparto | RC Extracontractual (3,500M), GAP Patrimonial (10% VA), Accidentes Personales (15M), Renta Diaria condicional | Balance protección-costo |
| Pesados Clásico | Camiones, Furgones, Chásis, Remolques, Camionetas cerradas de reparto | RC Extracontractual (2,000M), Sin GAP, Sin Accidentes Personales | Protección básica, precio competitivo |

### Definition of Done (DoD)

1. Todos los criterios de aceptación de los requisitos 1-15 verificados y aprobados.
2. Ofertas MVP (Premium, Estándar, Clásico) creadas y configuradas con todas sus coberturas y servicios.
3. Latencia de consulta de ofertas inferior a 200ms (p95) validada con prueba de carga a 500 peticiones concurrentes.
4. Integración con Motor_Tarifario funcional con respuesta en menos de 5 segundos.
5. Cobertura de tests unitarios superior al 80%.
6. Tests de integración para endpoints de ofertas, coberturas, listas de valores e historial.
7. Smoke tests E2E ejecutados post-deploy en cada ambiente.
8. SAST y SCA ejecutados sin vulnerabilidades críticas o altas.
9. Documentación OpenAPI/Swagger actualizada para todos los endpoints del Motor_Ofertas.
10. Logs estructurados con Correlation-ID verificados en CloudWatch.
11. Rate limiting, circuit breakers y auto-scaling validados en ambiente de staging.
12. RBAC verificado: Administrador con CRUD, Intermediario con solo lectura, Auditor con solo historial.
13. Control de concurrencia optimista validado con pruebas de escritura simultánea.
14. Revisión de código aprobada (mínimo 1 reviewer).
15. Deploy progresivo (blue-green) configurado para producción.
