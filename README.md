import streamlit as st
import pandas as pd

# Configuración de la página
st.set_page_config(page_title="Evaluación ISO 27001", page_icon="🔒", layout="wide")

# Datos del cuestionario
dominios = {
    "A.5 Gobernanza de Seguridad": [
        {
            "pregunta": "¿Existe una política documentada y aprobada de seguridad de la información?",
            "recomendaciones": {
                1: "Elaborar una política formal, documentarla y someterla a aprobación de la alta dirección.",
                2: "Verificar si la política está completa, actualizada y correctamente distribuida.",
                3: "Incluir responsables, objetivos claros y revisión periódica en la política.",
                4: "Asegurar que la política esté alineada con otras normativas internas y con ISO 27001.",
                5: "Mantener revisiones proactivas, difundirla ampliamente y auditar su cumplimiento."
            }
        },
        {
            "pregunta": "¿Se revisa la política de seguridad al menos una vez al año?",
            "recomendaciones": {
                1: "Establecer un cronograma anual obligatorio de revisión.",
                2: "Nombrar responsables y establecer fechas formales de revisión.",
                3: "Documentar revisiones pasadas y aplicar mejoras sugeridas.",
                4: "Asociar revisión a cambios de riesgos, incidentes o tecnología.",
                5: "Integrar revisión continua con métricas de desempeño y validación externa."
            }
        },
        {
            "pregunta": "¿La alta dirección participa en la gestión de la seguridad de la información?",
            "recomendaciones": {
                1: "Sensibilizar a la alta dirección sobre su rol en la seguridad.",
                2: "Involucrarla en reuniones de seguridad o asignarle funciones específicas.",
                3: "Solicitar su apoyo en decisiones clave y recursos.",
                4: "Alta dirección lidera o respalda proyectos de seguridad.",
                5: "Participa activamente y promueve la cultura de seguridad en toda la organización."
            }
        }
    ],
    "A.9 Control de Accesos": [
        {
            "pregunta": "¿Se revocan los accesos inmediatamente cuando un empleado deja la organización?",
            "recomendaciones": {
                1: "Implementar procedimientos obligatorios de revocación inmediata.",
                2: "Notificar de forma automática a TI ante bajas de personal.",
                3: "Mantener un registro de revocaciones y su tiempo de ejecución.",
                4: "Verificar el cumplimiento mediante auditorías internas.",
                5: "Automatizar la revocación mediante integración con RRHH y sistemas de acceso."
            }
        },
        {
            "pregunta": "¿Se utiliza autenticación multifactor (MFA) para accesos remotos?",
            "recomendaciones": {
                1: "Aplicar MFA en todos los accesos remotos de inmediato.",
                2: "Extender MFA a todo el personal, no solo usuarios críticos.",
                3: "Evaluar herramientas MFA más seguras y fáciles de usar.",
                4: "Implementar MFA en todos los sistemas de información críticos.",
                5: "Usar MFA adaptativo según ubicación, horario o dispositivo."
            }
        },
        {
            "pregunta": "¿Los accesos a los sistemas están definidos según roles o perfiles?",
            "recomendaciones": {
                1: "Aplicar el modelo de acceso basado en roles (RBAC).",
                2: "Revisar que cada usuario tenga los accesos mínimos necesarios.",
                3: "Documentar roles y permisos en un sistema centralizado.",
                4: "Auditar accesos regularmente y validar con los jefes de área.",
                5: "Automatizar asignación/revisión de roles según cambios de función."
            }
        }
    ],
    "A.12 Seguridad Operacional": [
        {
            "pregunta": "¿Se realizan respaldos automáticos de la información crítica?",
            "recomendaciones": {
                1: "Establecer respaldo automático diario/semanal de información crítica.",
                2: "Asegurar que toda la información crítica esté incluida en los respaldos.",
                3: "Supervisar logs de respaldo y configurar alertas ante fallos.",
                4: "Usar almacenamiento externo o en la nube para mayor seguridad.",
                5: "Implementar respaldo automatizado, cifrado y con redundancia geográfica."
            }
        },
        {
            "pregunta": "¿Se prueban periódicamente los respaldos para garantizar su restauración?",
            "recomendaciones": {
                1: "Programar pruebas de restauración al menos trimestralmente.",
                2: "Documentar resultados de las pruebas y revisar los errores detectados.",
                3: "Incluir distintas muestras de información y pruebas simuladas.",
                4: "Asociar las pruebas a los planes de continuidad de negocio.",
                5: "Realizar simulacros completos y documentar evidencia de restauración exitosa."
            }
        },
        {
            "pregunta": "¿Existe un plan de gestión de vulnerabilidades actualizado?",
            "recomendaciones": {
                1: "Crear un plan de gestión de vulnerabilidades documentado.",
                2: "Establecer revisiones periódicas del plan (mínimos semestrales).",
                3: "Ejecutar escaneos automáticos y manuales de vulnerabilidades.",
                4: "Clasificar vulnerabilidades y aplicar acciones correctivas en tiempos definidos.",
                5: "Integrar con herramientas de monitoreo y respuesta automática a incidentes."
            }
        }
    ]
}

# Función para mostrar las preguntas y capturar respuestas
def mostrar_cuestionario():
    st.title("🔒 Evaluación de Cumplimiento ISO 27001")
    st.markdown("""
    Esta herramienta le permite evaluar el nivel de cumplimiento de su organización con los controles de seguridad 
    de la información según la norma ISO 27001. Para cada pregunta, seleccione el nivel que mejor describa su situación actual.
    """)
    
    respuestas = {}
    
    for dominio, preguntas in dominios.items():
        st.header(f"Dominio: {dominio}")
        
        for i, pregunta_data in enumerate(preguntas, 1):
            pregunta = pregunta_data["pregunta"]
            key = f"{dominio}_{i}"
            
            st.subheader(f"{i}. {pregunta}")
            respuesta = st.radio(
                "Seleccione su nivel de cumplimiento:",
                options=[1, 2, 3, 4, 5],
                format_func=lambda x: f"{x} - {'No cumple' if x == 1 else 'Cumple parcialmente' if x == 2 else 'Cumple mínimamente' if x == 3 else 'Cumple bien' if x == 4 else 'Cumple y supera'}",
                key=key,
                horizontal=True
            )
            
            respuestas[key] = {
                "pregunta": pregunta,
                "respuesta": respuesta,
                "dominio": dominio
            }
    
    return respuestas

# Función para generar el reporte
def generar_reporte(respuestas):
    st.title("📊 Reporte de Evaluación")
    
    # Resumen por dominio
    st.header("Resumen por Dominio")
    
    datos = []
    for key, valor in respuestas.items():
        datos.append({
            "Dominio": valor["dominio"],
            "Pregunta": valor["pregunta"],
            "Nivel": valor["respuesta"],
            "Evaluación": "No cumple" if valor["respuesta"] == 1 
                         else "Cumple parcialmente" if valor["respuesta"] == 2 
                         else "Cumple mínimamente" if valor["respuesta"] == 3 
                         else "Cumple bien" if valor["respuesta"] == 4 
                         else "Cumple y supera"
        })
    
    df = pd.DataFrame(datos)
    
    # Mostrar tabla con todas las respuestas
    st.dataframe(df, hide_index=True, use_container_width=True)
    
    # Gráfico de barras por dominio
    st.subheader("Puntuación Promedio por Dominio")
    dominio_promedio = df.groupby("Dominio")["Nivel"].mean().reset_index()
    st.bar_chart(dominio_promedio, x="Dominio", y="Nivel")
    
    # Puntuación total
    puntuacion_total = df["Nivel"].sum()
    max_puntuacion = len(df) * 5
    porcentaje = (puntuacion_total / max_puntuacion) * 100
    
    st.subheader("Puntuación Total")
    col1, col2 = st.columns(2)
    col1.metric("Puntuación Obtenida", f"{puntuacion_total}/{max_puntuacion}")
    col2.metric("Porcentaje de Cumplimiento", f"{porcentaje:.1f}%")
    
    # Recomendaciones por pregunta
    st.header("🔍 Recomendaciones para Mejora")
    
    for key, valor in respuestas.items():
        dominio = valor["dominio"]
        pregunta = valor["pregunta"]
        respuesta = valor["respuesta"]
        
        # Buscar las recomendaciones para esta pregunta
        for preguntas_dominio in dominios[dominio]:
            if preguntas_dominio["pregunta"] == pregunta:
                recomendacion = preguntas_dominio["recomendaciones"][respuesta]
                break
        
        with st.expander(f"{pregunta} (Nivel: {respuesta})"):
            st.write(f"**Recomendación:** {recomendacion}")
            
            if respuesta < 5:
                st.write("**Para alcanzar el siguiente nivel:**")
                next_level = min(respuesta + 1, 5)
                st.write(preguntas_dominio["recomendaciones"][next_level])

# Página principal
def main():
    respuestas = mostrar_cuestionario()
    
    if st.button("Generar Reporte", type="primary"):
        generar_reporte(respuestas)

if __name__ == "__main__":
    main()
