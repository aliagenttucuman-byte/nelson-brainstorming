# Evaluación Ejecutiva: firecrawl-workflows

**Repo:** https://github.com/firecrawl/firecrawl-workflows  
**Stars:** 20 | **Forks:** 4 | **Issues abiertos:** 0  
**Licencia:** ISC | **Creado:** mayo 2026 | **Tamaño:** 29 KB  
**Descripción:** Ninguna (repo de skills/documentación)

---

## ¿Qué es?

Pack de skills/workflows para agentes de IA que usan **Firecrawl** (servicio de scraping web) a través de su CLI. No es librería ni engine — es documentación y prompts organizados en skills tipo:

- deep-research, seo-audit, lead-research, qa
- competitive-intel, company-directories, lead-gen
- knowledge-base, market-research, research-papers
- demo-walkthrough, shop, website-design-clone

Inspirado en los repos `anthropics/skills`, `anthropics/financial-services`, etc.

---

## Veredicto: NO. No aporta código reutilizable.

### Por qué NO:

1. **Es un repo de prompts/skills, no una librería.** No hay código Python/JS reutilizable para nuestro stack.
2. **Skills de terceros vs skills custom.** Nelson exige skills custom centralizadas en el orquestador. Usar un pack externo rompe la convención y la trazabilidad.
3. **20 estrellas, 1 mes de vida.** Muy verde. Sin comunidad que lo valide.
4. **Duplicación funcional:** ya tenemos `nelson-browser-agent` con Playwright para scraping y navegación. También tenemos `nelson-document-processing` para ingest de docs.
5. **Firecrawl como servicio SÍ es útil**, pero este repo solo te dice cómo usarlo. Eso lo podemos escribir nosotros en una skill propia si lo necesitamos.

### Cuándo revisar Firecrawl (no este repo):

- Necesitemos scraping a escala de cientos de URLs con extracción estructurada (LLM-based)
- Lead gen masivo desde directorios web
- Research competitivo automatizado sobre websites de rivals
- Queremos reemplazar Playwright por un servicio managed para ciertos casos

### Diferencia clave:

| | `firecrawl-workflows` | `firecrawl` servicio |
|---|---|---|
| Qué es | Pack de prompts/skills | API de scraping web + LLM extraction |
| ¿Nos sirve? | NO | SÍ, a evaluar |
| Alternativa propia | `nelson-browser-agent` | Playwright + OpenAI/Claude en nuestro backend |

---

## Conclusión

**Radar del repo: NO.** Es documentación de skills, no engine.  
**Radar de Firecrawl como servicio: SÍ, a evaluar** si surge caso de scraping masivo con extracción estructurada.
