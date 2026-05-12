<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import RegistrationModal from '@/components/RegistrationModal.vue'
import { captureFbParams } from '@/utils/fbclid'
import romanPhoto from '@/assets/team/image.png'

const router = useRouter()
const modalOpen = ref(false)
const IS_DEV = window.location.hostname === 'localhost'

const openModal = () => {
  if (!IS_DEV) {
    const disqAt = localStorage.getItem('os_disq_at')
    if (disqAt && Date.now() - Number(disqAt) < 48 * 60 * 60 * 1000) {
      router.push('/sin-espacio')
      return
    }
  }
  modalOpen.value = true
}

const stats = [
  {
    icon: 'fa-solid fa-dna',
    number: '+15K',
    text: 'Pacientes evaluados con criterio clínico en Medicina Regenerativa',
  },
  {
    icon: 'fa-solid fa-microscope',
    number: '20+',
    text: 'Años de experiencia guiando casos complejos de regeneración celular',
  },
  {
    icon: 'fa-solid fa-shield-heart',
    number: '20%',
    text: 'De las aplicaciones son aceptadas — priorizamos criterio sobre volumen',
  },
]

const pillars = [
  'Ya intentaste múltiples soluciones sin resultados sostenibles',
  'Estás cansado de controlar sin mejorar realmente',
  'Quieres entender qué está pasando de fondo en tu metabolismo',
  'Buscas una estrategia seria, integral y con criterio clínico',
]

const approach = [
  {
    num: '01',
    icon: 'fa-solid fa-dna',
    title: 'Análisis de Señalización Celular',
    body: 'No miramos solo glucosa. Evaluamos la resistencia a la insulina, inflamación crónica y capacidad regenerativa real de tu páncreas y tejidos metabólicos.',
  },
  {
    num: '02',
    icon: 'fa-solid fa-arrow-trend-up',
    title: 'Recalibración Metabólica Profunda',
    body: 'Diseñamos un protocolo integral para preparar tu cuerpo antes de cualquier intervención. Priming biológico para que las terapias tengan efecto real.',
  },
  {
    num: '03',
    icon: 'fa-solid fa-clock-rotate',
    title: 'Continuidad Biológica Sostenida',
    body: 'No se trata de una solución temporal. Construimos una estrategia de seguimiento para mantener la estabilidad metabólica en el tiempo, sin rebotes.',
  },
]

// Countdown urgency — bloque fijo de 6h anclado al reloj local (00, 06, 12, 18).
// Mismo valor para todos los visitantes en un instante dado → sensación de continuidad real.
const BLOCK_HOURS = 6
const hours = ref('00')
const minutes = ref('00')
const seconds = ref('00')
let interval: ReturnType<typeof setInterval>

const computeRemaining = () => {
  const now = new Date()
  const blockStartHour = Math.floor(now.getHours() / BLOCK_HOURS) * BLOCK_HOURS
  const blockStart = new Date(now)
  blockStart.setHours(blockStartHour, 0, 0, 0)
  const blockEndMs = blockStart.getTime() + BLOCK_HOURS * 3600 * 1000
  return Math.max(0, Math.floor((blockEndMs - now.getTime()) / 1000))
}

const tick = () => {
  const total = computeRemaining()
  hours.value   = String(Math.floor(total / 3600)).padStart(2, '0')
  minutes.value = String(Math.floor((total % 3600) / 60)).padStart(2, '0')
  seconds.value = String(total % 60).padStart(2, '0')
}

onMounted(() => {
  captureFbParams()
  tick()
  interval = setInterval(tick, 1000)
  startProofRotation()
})

onUnmounted(() => {
  clearInterval(interval)
  stopProofRotation()
})

// ── Social proof toast (FOMO) ──────────────────────────────────────────────
type Proof = { who: string; where: string; minutesAgo: number }

const PROOFS: Proof[] = [
  { who: 'Empresario Polanco',        where: 'CDMX',              minutesAgo: 3 },
  { who: 'María Fernanda L.',         where: 'Lomas',             minutesAgo: 7 },
  { who: 'Andrés Salazar',            where: 'Santa Fe',          minutesAgo: 12 },
  { who: 'Grupo Financiero G.',       where: 'Polanco',           minutesAgo: 18 },
  { who: 'Carolina M. (DM2)',         where: 'Interlomas',        minutesAgo: 22 },
  { who: 'Director Médico R.',        where: 'Anzures',           minutesAgo: 27 },
  { who: 'Familia De la Vega',        where: 'Bosques',           minutesAgo: 34 },
  { who: 'CEO Tech Start-up',         where: 'Santa Fe',          minutesAgo: 41 },
  { who: 'Roberto S. (DM2)',          where: 'Lomas Altas',       minutesAgo: 48 },
  { who: 'Patricia Andrade',          where: 'Polanco',           minutesAgo: 55 },
]

const proofVisible = ref(false)
const proofIndex = ref(0)
const currentProof = ref<Proof | null>(null)
let proofShowTimer: ReturnType<typeof setTimeout> | null = null
let proofHideTimer: ReturnType<typeof setTimeout> | null = null
let proofDismissed = false

const SHOW_AFTER_MS = 3000      // primera aparición
const VISIBLE_FOR_MS = 5000     // tiempo visible
const GAP_BETWEEN_MS = 2000     // pausa entre toasts (ciclo total ≈ 7s)

const showNextProof = () => {
  if (proofDismissed) return
  currentProof.value = PROOFS[proofIndex.value % PROOFS.length]
  proofIndex.value++
  proofVisible.value = true
  proofHideTimer = setTimeout(() => {
    proofVisible.value = false
    proofShowTimer = setTimeout(showNextProof, GAP_BETWEEN_MS)
  }, VISIBLE_FOR_MS)
}

const startProofRotation = () => {
  proofShowTimer = setTimeout(showNextProof, SHOW_AFTER_MS)
}

const stopProofRotation = () => {
  if (proofShowTimer) clearTimeout(proofShowTimer)
  if (proofHideTimer) clearTimeout(proofHideTimer)
  proofShowTimer = null
  proofHideTimer = null
}

const dismissProof = () => {
  proofDismissed = true
  proofVisible.value = false
  stopProofRotation()
}
</script>

<template>
  <div class="funnel">

    <!-- TOP BAR -->
    <header class="funnel__topbar">
      <h2 class="funnel__logo-text">POWERHOUSE BIOTECH</h2>
    </header>

    <!-- URGENCY BANNER (sticky) -->
    <div class="funnel__urgency" role="banner">
      <div class="funnel__urgency-info">
        <span class="funnel__urgency-dot" aria-hidden="true" />
        <i class="fa-solid fa-bolt funnel__urgency-icon" aria-hidden="true"></i>
        <span class="funnel__urgency-text">EVALUACIONES ABIERTAS — solo <strong>20% de casos son aceptados</strong>. Cierran en:</span>
        <div class="funnel__timer" aria-live="polite" aria-label="Tiempo restante">
          <span class="funnel__timer-block"><strong>{{ hours }}</strong><small>h</small></span>
          <span class="funnel__timer-sep" aria-hidden="true">:</span>
          <span class="funnel__timer-block"><strong>{{ minutes }}</strong><small>m</small></span>
          <span class="funnel__timer-sep" aria-hidden="true">:</span>
          <span class="funnel__timer-block"><strong>{{ seconds }}</strong><small>s</small></span>
        </div>
      </div>
      <button
        type="button"
        class="funnel__urgency-cta"
        aria-label="Aplicar para Evaluación de Diabetes Regenerativa"
        @click="openModal()"
      >
        EVALUAR MI CASO
        <span aria-hidden="true">→</span>
      </button>
    </div>

    <!-- SOCIAL PROOF TOAST (bottom-left, FOMO) -->
    <Transition name="proof-fade">
      <div v-if="proofVisible && currentProof" class="funnel__proof" role="status" aria-live="polite">
        <div class="funnel__proof-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-check"></i>
        </div>
        <div class="funnel__proof-body">
          <p class="funnel__proof-title">
            <strong>{{ currentProof.who }}</strong>
            <span>{{ currentProof.where }}</span>
          </p>
          <p class="funnel__proof-text">
            Acaba de aplicar para su <strong>Evaluación Metabólica para Diabetes</strong>
          </p>
          <p class="funnel__proof-meta">
            <i class="fa-solid fa-clock" aria-hidden="true"></i>
            Hace {{ currentProof.minutesAgo }} min
          </p>
        </div>
        <button
          type="button"
          class="funnel__proof-close"
          aria-label="Cerrar notificación"
          @click="dismissProof"
        >
          <i class="fa-solid fa-xmark" aria-hidden="true"></i>
        </button>
      </div>
    </Transition>

    <!-- HERO -->
    <section class="funnel__hero" aria-labelledby="funnel-headline">
      <div class="funnel__container">

        <p class="funnel__eyebrow">
          <i class="fa-solid fa-dna" aria-hidden="true"></i>
          PowerHouse Biotech · Diabetes Regenerativa · Polanco, CDMX
        </p>

        <h1 id="funnel-headline" class="funnel__headline">
          ¿Cansado de controlar tu diabetes sin ver mejoras reales?
          <span class="funnel__headline-accent">Un enfoque regenerativo que busca restaurar tu metabolismo</span>
        </h1>

        <p class="funnel__hero-sub">
          Protocolos avanzados con medicina regenerativa y seguimiento clínico para mejorar tu calidad de vida y estabilidad metabólica. Si llevas años controlando tu condición pero tu energía y tu metabolismo no responden, el problema no eres tú — es seguir tratando síntomas sin corregir el terreno biológico.
        </p>

        <ul class="funnel__pillars" role="list">
          <li v-for="p in pillars" :key="p" class="funnel__pillar">
            <i class="fa-solid fa-check" aria-hidden="true"></i>
            {{ p }}
          </li>
        </ul>

        <!-- Urgency callout (refuerzo en hero) -->
        <div class="funnel__urgency-callout" role="note">
          <i class="fa-solid fa-fire" aria-hidden="true"></i>
          <span>Para pacientes con diabetes decididos a saber si su metabolismo puede regenerarse — <strong>no a probar terapias a ciegas</strong>.</span>
        </div>

        <!-- VSL Gated Area -->
        <div class="funnel__vsl-wrap">
          <div class="funnel__vsl" @click="openModal()" role="button" aria-label="Ver video" tabindex="0">
            <div class="funnel__vsl-bg">
              <img src="https://res.cloudinary.com/dz6qozi9i/image/upload/v1778604773/fotos-roman/IMG_3901.jpg" class="funnel__vsl-thumb" alt="Juan Román Garza — PowerHouse Biotech" />
              <div class="funnel__vsl-blur-overlay"></div>
            </div>
            <div class="funnel__vsl-overlay">
              <div class="funnel__vsl-play">
                <i class="fa-solid fa-play" aria-hidden="true"></i>
              </div>
              <p class="funnel__vsl-caption">Juan Román Garza explica por qué tu diabetes puede estar bloqueada a nivel celular y cómo destapar la capacidad regenerativa de tu metabolismo</p>
            </div>
          </div>
        </div>

        <!-- CTA -->
        <div class="funnel__cta-wrap">
          <button class="funnel__cta-btn" @click="openModal()">
            <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
            QUIERO EVALUAR MI CASO
          </button>
          <p class="funnel__cta-sub">
            <i class="fa-solid fa-lock" aria-hidden="true"></i>
            Sin costo &nbsp;·&nbsp; Solo 20% aceptados &nbsp;·&nbsp; Confidencial
          </p>
        </div>

      </div>
    </section>

    <!-- STATS -->
    <section class="funnel__stats" aria-label="Resultados comprobados">
      <div class="funnel__container">
        <p class="funnel__section-label funnel__section-label--light">Casos metabólicos reales — resultados medibles</p>
        <div class="funnel__stats-grid">
          <div v-for="stat in stats" :key="stat.number" class="funnel__stat">
            <div class="funnel__stat-icon" aria-hidden="true">
              <i :class="stat.icon"></i>
            </div>
            <strong class="funnel__stat-number">{{ stat.number }}</strong>
            <p class="funnel__stat-text">{{ stat.text }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- PARA QUIÉN ES / NO ES -->
    <section class="funnel__audience" aria-labelledby="audience-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">¿Este proceso es para ti?</p>
        <h2 id="audience-heading" class="funnel__section-title">
          Para quién es y para quién NO es
        </h2>
        <div class="funnel__audience-grid">
          <div class="funnel__audience-col funnel__audience-col--for">
            <div class="funnel__audience-header">
              <i class="fa-solid fa-circle-check" aria-hidden="true"></i>
              <span>Este proceso es para personas que:</span>
            </div>
            <ul class="funnel__audience-list" role="list">
              <li>Ya intentaron múltiples soluciones sin resultados sostenibles</li>
              <li>Están cansadas de "controlar" la diabetes sin mejorar realmente</li>
              <li>Quieren entender qué está pasando de fondo en su metabolismo</li>
              <li>Están listas para tomar decisiones inteligentes, no solo obedecer inercias médicas</li>
            </ul>
          </div>
          <div class="funnel__audience-col funnel__audience-col--not">
            <div class="funnel__audience-header">
              <i class="fa-solid fa-circle-xmark" aria-hidden="true"></i>
              <span>Este NO es un proceso para:</span>
            </div>
            <ul class="funnel__audience-list" role="list">
              <li>Quien busca un atajo barato o una cura mágica</li>
              <li>Quien quiere seguir pateando el problema</li>
              <li>Quien prefiere anestesiar síntomas y llamarle a eso progreso</li>
              <li>Quien no está comprometido con su salud, solo con sus síntomas</li>
            </ul>
          </div>
        </div>
      </div>
    </section>

    <!-- PROBLEMA -->
    <section class="funnel__problem" aria-labelledby="problem-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">La verdad sobre la diabetes</p>
        <h2 id="problem-heading" class="funnel__section-title">
          La diabetes no es solo un número alto
        </h2>
        <p class="funnel__problem-intro">
          Es el resultado de un sistema que lleva tiempo deteriorándose:
        </p>
        <div class="funnel__problem-grid">
          <div class="funnel__problem-item">
            <i class="fa-solid fa-bolt funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Resistencia a la insulina</strong>
              <p>Tus células dejaron de responder a la insulina. La glucosa se acumula en sangre mientras tus órganos se quedan sin energía.</p>
            </div>
          </div>
          <div class="funnel__problem-item">
            <i class="fa-solid fa-droplet funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Inflamación crónica</strong>
              <p>Un estado inflamatorio silencioso daña tus tejidos y órganos. Es la base de las complicaciones: neuropatía, retinopatía, enfermedad renal.</p>
            </div>
          </div>
          <div class="funnel__problem-item">
            <i class="fa-solid fa-chart-line funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Desregulación metabólica</strong>
              <p>Tu metabolismo energético está desordenado. El cuerpo no usa la glucosa de forma eficiente, tu energía celular está comprometida.</p>
            </div>
          </div>
          <div class="funnel__problem-item">
            <i class="fa-solid fa-flask funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Deterioro celular progresivo</strong>
              <p>Tu capacidad de recuperación biológica está limitada. El daño avanza aunque los números en el laboratorio parezcan estables.</p>
            </div>
          </div>
        </div>
        <p class="funnel__problem-footer">
          La diabetes no solo altera tus análisis — altera tu libertad, tu tranquilidad y la dinámica de tu familia. Tu fatiga, tu dificultad para bajar de peso, tu dependencia creciente de medicación y la sensación de que tu cuerpo ya no responde no son casualidad.
        </p>
        <div class="funnel__cta-inline">
          <button class="funnel__cta-btn" @click="openModal()">
            <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
            QUIERO EVALUAR MI CASO
          </button>
        </div>
      </div>
    </section>

    <!-- EVALUACIÓN DE VIABILIDAD REGENERATIVA -->
    <section class="funnel__method" aria-labelledby="method-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Evaluación de Viabilidad Regenerativa™</p>
        <h2 id="method-heading" class="funnel__section-title">
          Creado para responder una sola pregunta: ¿tu cuerpo realmente puede mejorar?
        </h2>
        <div class="funnel__method-grid">
          <div v-for="m in approach" :key="m.num" class="funnel__method-card">
            <div class="funnel__method-num" aria-hidden="true">{{ m.num }}</div>
            <div class="funnel__method-icon" aria-hidden="true">
              <i :class="m.icon"></i>
            </div>
            <h3 class="funnel__method-title">{{ m.title }}</h3>
            <p class="funnel__method-body">{{ m.body }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- PROTOCOLO 4 DIMENSIONES -->
    <section class="funnel__dimensions" aria-labelledby="dimensions-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Protocolo de Precisión</p>
        <h2 id="dimensions-heading" class="funnel__section-title">
          Evaluación en 4 Dimensiones — máxima precisión biológica
        </h2>
        <p class="funnel__dimensions-intro">Cada fase está interconectada. No son pasos aislados, es un ecosistema de recuperación.</p>
        <div class="funnel__dimensions-grid">
          <div class="funnel__dim-card">
            <div class="funnel__dim-num">01</div>
            <h3 class="funnel__dim-title">Evaluación del Terreno Biológico</h3>
            <p class="funnel__dim-body">No miramos solo laboratorios. Evaluamos inflamación, estrés oxidativo y capacidad regenerativa real de tu metabolismo.</p>
          </div>
          <div class="funnel__dim-card">
            <div class="funnel__dim-num">02</div>
            <h3 class="funnel__dim-title">Priming Metabólico</h3>
            <p class="funnel__dim-body">Preparamos tu cuerpo para que sea capaz de recibir y procesar las terapias avanzadas. Un cuerpo receptivo es la clave del éxito.</p>
          </div>
          <div class="funnel__dim-card">
            <div class="funnel__dim-num">03</div>
            <h3 class="funnel__dim-title">Intervención de Precisión</h3>
            <p class="funnel__dim-body">Aplicamos el protocolo integral (IV, Nebulizado, Oral) diseñado específicamente para tu caso y tu firma biológica.</p>
          </div>
          <div class="funnel__dim-card">
            <div class="funnel__dim-num">04</div>
            <h3 class="funnel__dim-title">Seguimiento Biológico</h3>
            <p class="funnel__dim-body">Monitoreamos la respuesta de tus células y ajustamos el protocolo en tiempo real para mantener la estabilidad metabólica.</p>
          </div>
        </div>
      </div>
    </section>

    <!-- 3 VÍAS DE ACCESO -->
    <section class="funnel__pathways" aria-labelledby="pathways-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Abordaje en 3 Vías</p>
        <h2 id="pathways-heading" class="funnel__section-title">
          No creemos en soluciones de una sola vía
        </h2>
        <p class="funnel__pathways-intro">La complejidad de la diabetes requiere un abordaje multifactorial, preciso y sostenido.</p>
        <div class="funnel__pathways-grid">
          <div class="funnel__path-card funnel__path-card--phase">
            <div class="funnel__path-badge">FASE 0</div>
            <h3 class="funnel__path-title">Priming Biológico</h3>
            <p class="funnel__path-body">Preparamos tu terreno metabólico antes de cualquier intervención mayor. Un cuerpo receptivo es la clave del éxito.</p>
          </div>
          <div class="funnel__path-card">
            <div class="funnel__path-icon" aria-hidden="true">
              <i class="fa-solid fa-syringe"></i>
            </div>
            <span class="funnel__path-tag">SISTÉMICA</span>
            <h3 class="funnel__path-title">Vía Intravenosa (IV)</h3>
            <p class="funnel__path-body">Acción sistémica profunda para modular la inflamación y potenciar la recuperación general a través del torrente sanguíneo.</p>
            <span class="funnel__path-tech">TECNOLOGÍA PHB GRADO MÉDICO</span>
          </div>
          <div class="funnel__path-card">
            <div class="funnel__path-icon" aria-hidden="true">
              <i class="fa-solid fa-lungs"></i>
            </div>
            <span class="funnel__path-tag">ABSORCIÓN RÁPIDA</span>
            <h3 class="funnel__path-title">Vía Nebulizada</h3>
            <p class="funnel__path-body">Ruta complementaria de alta biodisponibilidad para soporte metabólico directo, ideal para optimizar la oxigenación celular.</p>
            <span class="funnel__path-tech">TECNOLOGÍA PHB GRADO MÉDICO</span>
          </div>
          <div class="funnel__path-card">
            <div class="funnel__path-icon" aria-hidden="true">
              <i class="fa-solid fa-capsules"></i>
            </div>
            <span class="funnel__path-tag">CONTINUIDAD</span>
            <h3 class="funnel__path-title">Vía Oral</h3>
            <p class="funnel__path-body">Mantenimiento y soporte biológico constante para sostener los resultados en el tiempo y estabilizar el terreno metabólico.</p>
            <span class="funnel__path-tech">TECNOLOGÍA PHB GRADO MÉDICO</span>
          </div>
        </div>
        <div class="funnel__cta-inline">
          <button class="funnel__cta-btn" @click="openModal()">
            <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
            QUIERO EVALUAR MI CASO
          </button>
        </div>
      </div>
    </section>

    <!-- CASE STUDIES -->
    <section class="funnel__cases" aria-labelledby="cases-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Resultados que hablan</p>
        <h2 id="cases-heading" class="funnel__section-title">
          Casos reales, evaluados bajo criterio de Viabilidad Regenerativa™
        </h2>
        <div class="funnel__cases-grid">
          <div class="funnel__case-card">
            <div class="funnel__case-header">
              <i class="fa-solid fa-file-waveform" aria-hidden="true"></i>
              <span>Diabetes Tipo 2 + Fatiga</span>
            </div>
            <div class="funnel__case-detail">
              <div class="funnel__case-context">
                <strong>CONTEXTO</strong>
                <p>Paciente de 54 años con diagnóstico de 12 años. Dependencia de metformina e insulina con picos constantes de glucosa.</p>
              </div>
              <div class="funnel__case-report">
                <strong>DECIDE™ REPORT</strong>
                <p>Evaluación EVR™ identificó resistencia periférica severa y un terreno biológico altamente acidificado.</p>
              </div>
              <div class="funnel__case-outcome">
                <strong>DESENLACE PHB™</strong>
                <p>Reducción del 70% en requerimiento de insulina | Estabilización de niveles basales en 30 días | Recuperación total de claridad mental</p>
              </div>
            </div>
          </div>
          <div class="funnel__case-card">
            <div class="funnel__case-header">
              <i class="fa-solid fa-brain" aria-hidden="true"></i>
              <span>Neuropatía Incipiente</span>
            </div>
            <div class="funnel__case-detail">
              <div class="funnel__case-context">
                <strong>CONTEXTO</strong>
                <p>Paciente con dolor neuropático en extremidades. Evolución tórpida a pesar de control farmacológico convencional.</p>
              </div>
              <div class="funnel__case-report">
                <strong>DECIDE™ REPORT</strong>
                <p>Se detectó microinflamación neurovascular y niveles críticos de estrés oxidativo tisular.</p>
              </div>
              <div class="funnel__case-outcome">
                <strong>DESENLACE PHB™</strong>
                <p>Remisión del dolor en un 85% | Mejora medible en la conducción nerviosa | Retorno a actividad física regular</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- BENEFITS -->
    <section class="funnel__benefits" aria-labelledby="benefits-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">No solo mejores niveles</p>
        <h2 id="benefits-heading" class="funnel__section-title">
          Una mejor vida
        </h2>
        <div class="funnel__benefits-grid">
          <div class="funnel__benefit-card">
            <div class="funnel__benefit-icon" aria-hidden="true">
              <i class="fa-solid fa-sun"></i>
            </div>
            <h3 class="funnel__benefit-title">Claridad y Energía</h3>
            <p class="funnel__benefit-body">Recupera la vitalidad que el desorden metabólico te había robado. Despierta con la mente despejada y el cuerpo listo para el día.</p>
            <span class="funnel__benefit-tag">EVOLUCIÓN BIOLÓGICA</span>
          </div>
          <div class="funnel__benefit-card">
            <div class="funnel__benefit-icon" aria-hidden="true">
              <i class="fa-solid fa-scale-balanced"></i>
            </div>
            <h3 class="funnel__benefit-title">Estabilidad Biológica</h3>
            <p class="funnel__benefit-body">Un sistema que funciona en equilibrio, sin picos ni caídas drásticas. Seguridad interna en cada proceso fisiológico.</p>
            <span class="funnel__benefit-tag">EVOLUCIÓN BIOLÓGICA</span>
          </div>
          <div class="funnel__benefit-card">
            <div class="funnel__benefit-icon" aria-hidden="true">
              <i class="fa-solid fa-hand-fist"></i>
            </div>
            <h3 class="funnel__benefit-title">Libertad de Acción</h3>
            <p class="funnel__benefit-body">Deja de vivir en función de tu condición y empieza a vivir en función de tus metas. El control real vuelve a tus manos.</p>
            <span class="funnel__benefit-tag">EVOLUCIÓN BIOLÓGICA</span>
          </div>
        </div>
      </div>
    </section>

    <!-- DIFFERENTIATORS -->
    <section class="funnel__diffs" aria-labelledby="diffs-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">La diferencia está en el criterio biológico</p>
        <h2 id="diffs-heading" class="funnel__section-title">
          Precisión molecular y honestidad clínica
        </h2>
        <div class="funnel__diffs-grid">
          <div class="funnel__diff-card">
            <div class="funnel__diff-stat">98%</div>
            <h3 class="funnel__diff-title">Precisión en Diagnóstico Molecular</h3>
            <p class="funnel__diff-body">No silenciamos síntomas. Resolvemos el desequilibrio biológico que los genera a través de una lectura profunda de tu terreno celular.</p>
            <span class="funnel__diff-tag">Criterio Clínico Superior</span>
          </div>
          <div class="funnel__diff-card">
            <div class="funnel__diff-icon" aria-hidden="true">
              <i class="fa-solid fa-rotate"></i>
            </div>
            <h3 class="funnel__diff-title">Intervención Multimodal</h3>
            <p class="funnel__diff-body">Cada cuerpo es único. Tu tratamiento integra vías IV, nebulizadas y orales diseñadas exclusivamente para tu firma biológica.</p>
            <span class="funnel__diff-tag">Protocolo 360° Real</span>
          </div>
          <div class="funnel__diff-card">
            <div class="funnel__diff-icon" aria-hidden="true">
              <i class="fa-solid fa-microscope"></i>
            </div>
            <h3 class="funnel__diff-title">Ciencia Regenerativa</h3>
            <p class="funnel__diff-body">Utilizamos las terapias celulares y exosomales más avanzadas, con respaldo clínico internacional y protocolos de seguridad Grado A.</p>
            <span class="funnel__diff-tag">Tecnología de Vanguardia</span>
          </div>
        </div>
      </div>
    </section>

    <!-- AUTHORITY — Juan Román Garza -->
    <section class="funnel__authority" aria-labelledby="authority-heading">
      <div class="funnel__container funnel__authority-inner">
        <div class="funnel__authority-photo-wrap">
          <div class="funnel__authority-avatar" aria-hidden="true">
            <img :src="romanPhoto" alt="Juan Román Garza — PowerHouse Biotech" class="funnel__authority-img" />
          </div>
        </div>
        <div class="funnel__authority-content">
          <p class="funnel__authority-eyebrow">Tu especialista evaluador</p>
          <h2 id="authority-heading" class="funnel__authority-name">Juan Román Garza</h2>
          <p class="funnel__authority-role">Especialista en Medicina Regenerativa · PowerHouse Biotech</p>
          <p class="funnel__authority-bio">
            Con más de dos décadas dedicadas a la medicina regenerativa, Juan Román Garza
            ha evaluado a miles de pacientes con trastornos metabólicos para determinar si su
            cuerpo está listo para regenerarse. Su enfoque no es vender terapias — es aplicar
            criterio clínico honesto para decirte si la regeneración metabólica es viable en tu caso.
            Ha ayudado a cientos de pacientes con diabetes tipo 2 en Polanco, Lomas, Santa Fe e
            Interlomas a recuperar su calidad de vida.
          </p>
          <ul class="funnel__authority-creds" role="list">
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> +15,000 pacientes evaluados con criterio regenerativo</li>
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> Interpretación cruzada de biomarcadores y mapeo celular</li>
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> Solo acepta pacientes con probabilidad real de respuesta</li>
          </ul>
        </div>
      </div>
    </section>

    <!-- CTA FINAL -->
    <section class="funnel__cta-final" aria-labelledby="cta-final-heading">
      <div class="funnel__container">
        <h2 id="cta-final-heading" class="funnel__cta-final-title">
          ¿Estás listo para dejar de controlar y empezar a resolver?
        </h2>
        <p class="funnel__cta-final-sub">
          No todos los casos son aptos. Pero todos merecen una respuesta honesta.
          Juan Román Garza evaluará tu caso para determinar si la regeneración metabólica
          es viable para ti. Sin costo, sin compromiso. Solo criterio clínico honesto.
        </p>
        <button class="funnel__cta-btn" @click="openModal()">
          <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
          QUIERO EVALUAR MI CASO
        </button>
        <p class="funnel__cta-sub">
          <i class="fa-solid fa-lock" aria-hidden="true"></i>
          100% gratuito &nbsp;·&nbsp; Sin compromiso &nbsp;·&nbsp; Cupos limitados
        </p>
      </div>
    </section>

    <!-- FOOTER -->
    <footer class="funnel__footer">
      <div class="funnel__container funnel__footer-inner">
        <h2 class="funnel__footer-logo-text">POWERHOUSE BIOTECH</h2>
        <nav class="funnel__footer-links" aria-label="Legal">
          <RouterLink to="/politicas-privacidad">Política de Privacidad</RouterLink>
          <RouterLink to="/aviso-legal">Aviso Legal</RouterLink>
        </nav>
        <p class="funnel__footer-copy">
          © {{ new Date().getFullYear() }} POWERHOUSE BIOTECH. Todos los derechos reservados.
        </p>
      </div>
    </footer>

  </div>

  <RegistrationModal :open="modalOpen" @close="modalOpen = false" />
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.funnel {
  background: #ffffff;
  color: colors.$OS-DARK;
  min-height: 100vh;
  font-family: fonts.$font-secondary;

  &__container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 1.5rem;
    @media (min-width: 768px) { padding: 0 2rem; }
  }
}

// ── Top bar ──────────────────────────────────────────────────────────────────
.funnel__topbar {
  background: #ffffff;
  border-bottom: 1px solid #E8EDF5;
  padding: 0.9rem 1.5rem;
  display: flex;
  justify-content: center;
  box-shadow: 0 1px 12px rgba(0, 0, 0, 0.06);
}

.funnel__logo {
  height: 38px;
  width: auto;
  object-fit: contain;
}

// ── Urgency banner ───────────────────────────────────────────────────────────
.funnel__urgency {
  position: sticky;
  top: 0;
  z-index: 50;
  background: linear-gradient(90deg, colors.$AB-URGENT 0%, colors.$AB-URGENT-DARK 100%);
  color: #ffffff;
  padding: 0.65rem 0.85rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  flex-wrap: wrap;
  font-family: fonts.$font-interface;
  font-size: 0.82rem;
  font-weight: 700;
  letter-spacing: 0.03em;
  border-bottom: 2px solid colors.$AB-URGENT-LIGHT;
  box-shadow: 0 2px 12px rgba(colors.$AB-URGENT, 0.35);
  text-transform: uppercase;

  @media (min-width: 768px) {
    padding: 0.85rem 1.5rem;
    font-size: 0.95rem;
    justify-content: space-between;
    flex-wrap: nowrap;
  }
}

.funnel__urgency-info {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  flex-wrap: wrap;
  justify-content: center;

  @media (min-width: 768px) {
    flex-wrap: nowrap;
  }
}

.funnel__urgency-cta {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  background: #ffffff;
  color: colors.$AB-URGENT-DARK;
  border: 2px solid colors.$AB-URGENT-LIGHT;
  border-radius: 999px;
  padding: 0.45rem 1rem;
  font-family: fonts.$font-accent;
  font-size: 0.78rem;
  font-weight: 900;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  cursor: pointer;
  white-space: nowrap;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.25);
  animation: cta-bounce 2.4s ease-in-out infinite;
  transition: transform 0.15s ease, box-shadow 0.2s ease, background 0.2s ease;

  span { font-size: 0.95rem; transition: transform 0.18s ease; }

  &:hover {
    background: colors.$AB-URGENT-BG;
    transform: translateY(-1px);
    box-shadow: 0 6px 18px rgba(0, 0, 0, 0.3);
    span { transform: translateX(3px); }
  }

  &:active { transform: translateY(0); }

  @media (min-width: 768px) {
    padding: 0.55rem 1.2rem;
    font-size: 0.85rem;
  }
}

@keyframes cta-bounce {
  0%, 100% { box-shadow: 0 4px 14px rgba(0, 0, 0, 0.25); transform: scale(1); }
  50%      { box-shadow: 0 6px 20px rgba(0, 0, 0, 0.4); transform: scale(1.04); }
}

// ── Social proof toast (FOMO bottom-left) ────────────────────────────────────
.funnel__proof {
  position: fixed;
  bottom: 1rem;
  left: 1rem;
  z-index: 55;
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  background: #ffffff;
  border: 1px solid rgba(colors.$AB-FOREST, 0.08);
  border-left: 4px solid colors.$AB-SAGE;
  border-radius: 12px;
  padding: 0.75rem 0.9rem 0.75rem 0.85rem;
  box-shadow: 0 12px 36px rgba(0, 0, 0, 0.18);
  max-width: 320px;
  font-family: fonts.$font-secondary;

  @media (min-width: 768px) {
    bottom: 1.5rem;
    left: 1.5rem;
    max-width: 360px;
    padding: 0.9rem 1rem 0.9rem 0.95rem;
  }
}

.funnel__proof-icon {
  flex-shrink: 0;
  width: 34px;
  height: 34px;
  border-radius: 50%;
  background: rgba(colors.$AB-SAGE, 0.14);
  color: colors.$AB-SAGE;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.05rem;
  margin-top: 2px;
}

.funnel__proof-body {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.funnel__proof-title {
  margin: 0;
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
  gap: 6px;
  font-family: fonts.$font-interface;
  font-size: 0.86rem;
  line-height: 1.3;
  color: colors.$AB-FOREST;

  strong { font-weight: 800; }
  span { font-size: 0.74rem; color: rgba(colors.$AB-FOREST, 0.55); font-weight: 500; }
}

.funnel__proof-text {
  margin: 0;
  font-size: 0.82rem;
  line-height: 1.35;
  color: rgba(colors.$AB-FOREST, 0.78);

  strong { color: colors.$AB-EARTH; font-weight: 700; }
}

.funnel__proof-meta {
  margin: 4px 0 0;
  font-size: 0.7rem;
  color: rgba(colors.$AB-FOREST, 0.45);
  display: inline-flex;
  align-items: center;
  gap: 4px;

  i { font-size: 0.66rem; }
}

.funnel__proof-close {
  position: absolute;
  top: 6px;
  right: 6px;
  width: 22px;
  height: 22px;
  border-radius: 50%;
  border: none;
  background: transparent;
  color: rgba(colors.$AB-FOREST, 0.4);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.78rem;
  transition: background 0.15s, color 0.15s;

  &:hover { background: rgba(colors.$AB-FOREST, 0.08); color: colors.$AB-FOREST; }
}

.proof-fade-enter-active {
  transition: opacity 0.32s ease, transform 0.42s cubic-bezier(0.34, 1.4, 0.64, 1);
}
.proof-fade-leave-active {
  transition: opacity 0.22s ease, transform 0.25s ease;
}
.proof-fade-enter-from {
  opacity: 0;
  transform: translateY(14px) translateX(-8px);
}
.proof-fade-leave-to {
  opacity: 0;
  transform: translateY(8px);
}

.funnel__urgency-icon {
  color: colors.$AB-URGENT-LIGHT;
  font-size: 1.1rem;
  filter: drop-shadow(0 0 6px rgba(colors.$AB-URGENT-LIGHT, 0.6));
  animation: bolt-flash 1.8s infinite;
}

@keyframes bolt-flash {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.55; transform: scale(0.92); }
}

.funnel__urgency-text {
  strong { font-weight: 900; color: #FFE4E4; letter-spacing: 0.04em; }
}

.funnel__urgency-dot {
  width: 9px;
  height: 9px;
  border-radius: 50%;
  background: colors.$AB-URGENT-LIGHT;
  flex-shrink: 0;
  animation: dot-pulse 1.5s infinite;
  box-shadow: 0 0 0 0 rgba(colors.$AB-URGENT-LIGHT, 0.6);
}

@keyframes dot-pulse {
  0% { box-shadow: 0 0 0 0 rgba(252, 165, 165, 0.7); transform: scale(1); }
  70% { box-shadow: 0 0 0 8px rgba(252, 165, 165, 0); transform: scale(1.1); }
  100% { box-shadow: 0 0 0 0 rgba(252, 165, 165, 0); transform: scale(1); }
}

.funnel__timer {
  display: flex;
  align-items: center;
  gap: 0.2rem;
  background: rgba(0, 0, 0, 0.18);
  padding: 0.25rem 0.6rem;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.funnel__timer-block {
  display: flex;
  align-items: baseline;
  gap: 2px;
  strong {
    font-size: 1.6rem;
    font-weight: 900;
    line-height: 1;
    font-variant-numeric: tabular-nums;
    color: #ffffff;

    @media (min-width: 768px) {
      font-size: 2rem;
    }
  }
  small {
    font-size: 0.72rem;
    opacity: 0.85;
    font-weight: 700;
    text-transform: uppercase;
  }
}

.funnel__timer-sep {
  font-weight: 900;
  font-size: 1.4rem;
  opacity: 0.7;
  padding: 0 2px;
  line-height: 1;

  @media (min-width: 768px) {
    font-size: 1.7rem;
  }
}

// ── Urgency callout (hero) ───────────────────────────────────────────────────
.funnel__urgency-callout {
  display: flex;
  align-items: center;
  gap: 0.65rem;
  background: colors.$AB-URGENT-BG;
  border-left: 4px solid colors.$AB-URGENT;
  border-radius: 8px;
  padding: 0.85rem 1rem;
  margin: 1.25rem 0 1.5rem;
  font-family: fonts.$font-interface;
  font-size: 0.92rem;
  color: colors.$AB-FOREST;
  font-weight: 600;

  i {
    color: colors.$AB-URGENT;
    font-size: 1.15rem;
    flex-shrink: 0;
  }

  strong {
    color: colors.$AB-URGENT-DARK;
    font-weight: 800;
  }

  @media (min-width: 768px) {
    font-size: 1rem;
    padding: 1rem 1.25rem;
  }
}

// ── Hero ─────────────────────────────────────────────────────────────────────
.funnel__hero {
  padding: 3.5rem 0 3rem;
  background: linear-gradient(180deg, #EEF4FF 0%, #ffffff 70%);
}

.funnel__eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(colors.$OS-NAVY, 0.06);
  border: 1px solid rgba(colors.$OS-NAVY, 0.14);
  border-radius: 999px;
  padding: 0.35rem 0.85rem;
  color: colors.$OS-NAVY;
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  margin: 0 0 1.4rem;
  i { font-size: 0.8rem; }
}

.funnel__headline {
  @include fonts.heading-font(800);
  font-size: clamp(2rem, 5vw, 3.1rem);
  line-height: 1.15;
  color: colors.$OS-DARK;
  margin: 0 0 1.5rem;
  letter-spacing: -0.025em;

  &-accent { color: colors.$OS-RED; }
}

.funnel__vsl-player-container {
  width: 100%;
  border-radius: 24px;
  overflow: hidden;
  border: 1px solid rgba(colors.$AB-WOOD, 0.2);
  box-shadow: 0 40px 100px -20px rgba(0,0,0,0.4);
  background: #000;
  line-height: 0;

  @media (max-width: 768px) {
    border-radius: 12px;
  }
}

.funnel__pillars {
  list-style: none;
  padding: 0;
  margin: 0 0 2rem;
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.funnel__pillar {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  font-size: 0.93rem;
  color: #3A4F6A;

  i {
    color: colors.$OS-BLUE;
    font-size: 0.82rem;
    flex-shrink: 0;
  }
}

// ── VSL ──────────────────────────────────────────────────────────────────────
.funnel__vsl-wrap { margin-bottom: 2rem; }

.funnel__vsl {
  position: relative;
  width: 100%;
  aspect-ratio: 16 / 9;
  border-radius: 16px;
  overflow: hidden;
  cursor: pointer;
  border: 2px solid #D8E6F5;
  box-shadow: 0 8px 40px rgba(0, 63, 125, 0.12);
  transition: transform 0.2s ease, box-shadow 0.2s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 16px 48px rgba(0, 63, 125, 0.2);
  }
  &:focus-visible {
    outline: 3px solid colors.$OS-BLUE;
    outline-offset: 2px;
  }
}

.funnel__vsl-bg {
  position: absolute;
  inset: 0;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
}

.funnel__vsl-thumb {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: blur(8px) brightness(0.6);
  transform: scale(1.1); // Avoid white edges from blur
  transition: filter 0.4s ease, transform 0.4s ease, brightness 0.4s ease;

  .funnel__vsl:hover & {
    filter: blur(4px) brightness(0.75);
    transform: scale(1.05);
  }
}

.funnel__vsl-blur-overlay {
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, rgba(colors.$OS-NAVY, 0.2) 0%, rgba(colors.$OS-NAVY, 0.6) 100%);
  z-index: 1;
}

.funnel__vsl-watermark {
  position: relative;
  z-index: 2;
  height: 60px;
  width: auto;
  opacity: 0.15;
  filter: brightness(0) invert(1);
}

.funnel__vsl-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1.25rem;
}

.funnel__vsl-play {
  width: 76px;
  height: 76px;
  border-radius: 50%;
  background: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.35);
  transition: transform 0.2s ease;

  .funnel__vsl:hover & { transform: scale(1.1); }

  i {
    color: colors.$OS-RED;
    font-size: 1.7rem;
    margin-left: 5px;
  }
}

.funnel__vsl-caption {
  color: #ffffff;
  font-size: 0.88rem;
  font-weight: 600;
  text-align: center;
  padding: 0 2rem;
  text-shadow: 0 1px 6px rgba(0, 0, 0, 0.5);
  max-width: 420px;
}

// ── CTA ──────────────────────────────────────────────────────────────────────
.funnel__cta-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
}

.funnel__cta-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  background: colors.$OS-RED;
  color: #ffffff;
  border: none;
  border-radius: 12px;
  padding: 1.1rem 2.5rem;
  font-family: fonts.$font-accent;
  font-size: 1rem;
  font-weight: 800;
  letter-spacing: 0.05em;
  cursor: pointer;
  width: 100%;
  max-width: 480px;
  transition: background 0.2s ease, transform 0.15s ease, box-shadow 0.2s ease;
  box-shadow: 0 4px 20px rgba(204, 0, 0, 0.35);

  &:hover {
    background: #AA0000;
    transform: translateY(-1px);
    box-shadow: 0 8px 28px rgba(204, 0, 0, 0.45);
  }
  &:active { transform: translateY(0); }
}

.funnel__cta-sub {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  font-size: 0.78rem;
  color: #8A9BB5;
  margin: 0;
  i { font-size: 0.72rem; }
}

// ── Stats ────────────────────────────────────────────────────────────────────
.funnel__stats {
  background: colors.$OS-NAVY;
  padding: 3rem 0;
}

.funnel__section-label {
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: colors.$OS-NAVY;
  margin: 0 0 1rem;

  &--light { color: rgba(#ffffff, 0.55); }
}

.funnel__stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
  @media (max-width: 580px) {
    grid-template-columns: 1fr;
    gap: 1.75rem;
  }
}

.funnel__stat { text-align: center; }

.funnel__stat-icon {
  font-size: 1.6rem;
  color: rgba(#ffffff, 0.35);
  margin-bottom: 0.5rem;
}

.funnel__stat-number {
  display: block;
  @include fonts.heading-font(800);
  font-size: 2.6rem;
  color: #ffffff;
  line-height: 1;
  margin-bottom: 0.4rem;
  letter-spacing: -0.03em;
}

.funnel__stat-text {
  font-size: 0.83rem;
  color: rgba(#ffffff, 0.7);
  line-height: 1.45;
  margin: 0;
}

// ── Problem ──────────────────────────────────────────────────────────────────
.funnel__problem {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__section-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.6rem, 3.5vw, 2.2rem);
  color: colors.$OS-DARK;
  margin: 0.25rem 0 2rem;
  line-height: 1.2;
  letter-spacing: -0.02em;
}

.funnel__problem-grid {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.funnel__problem-item {
  display: flex;
  gap: 1rem;
  align-items: flex-start;
  padding: 1.25rem;
  background: #F9FBFF;
  border: 1px solid #E4EDF7;
  border-radius: 12px;

  strong {
    display: block;
    color: colors.$OS-DARK;
    font-size: 0.93rem;
    font-weight: 700;
    margin-bottom: 0.2rem;
  }
  p {
    font-size: 0.86rem;
    color: #4A5F7A;
    line-height: 1.5;
    margin: 0;
  }
}

.funnel__problem-icon {
  font-size: 1.2rem;
  color: colors.$OS-RED;
  flex-shrink: 0;
  margin-top: 2px;
}

// ── Methodology ──────────────────────────────────────────────────────────────
.funnel__method {
  padding: 4rem 0;
  background: #F5F8FF;
}

.funnel__method-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__method-card {
  background: #ffffff;
  border: 1px solid #E4EDF7;
  border-radius: 16px;
  padding: 1.75rem 1.5rem;
  position: relative;
  box-shadow: 0 2px 12px rgba(0, 63, 125, 0.05);
}

.funnel__method-num {
  position: absolute;
  top: 1rem;
  right: 1.25rem;
  @include fonts.heading-font(800);
  font-size: 2.5rem;
  color: rgba(colors.$OS-NAVY, 0.07);
  line-height: 1;
  user-select: none;
}

.funnel__method-icon {
  width: 44px;
  height: 44px;
  border-radius: 10px;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1rem;
  i { color: #ffffff; font-size: 1.1rem; }
}

.funnel__method-title {
  @include fonts.heading-font(700);
  font-size: 0.97rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__method-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0;
}

// ── Testimonial ──────────────────────────────────────────────────────────────
.funnel__testimonial {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__testimonial-card {
  background: #F5F8FF;
  border: 1px solid rgba(colors.$OS-NAVY, 0.1);
  border-left: 4px solid colors.$OS-NAVY;
  border-radius: 16px;
  padding: 2rem;
  max-width: 720px;
  margin: 0 auto;
  box-shadow: 0 4px 24px rgba(0, 63, 125, 0.07);
}

.funnel__testimonial-quote {
  font-size: 2.2rem;
  color: rgba(colors.$OS-NAVY, 0.12);
  display: block;
  margin-bottom: 0.75rem;
  line-height: 1;
}

.funnel__testimonial-text {
  font-size: 1.1rem;
  color: colors.$OS-DARK;
  line-height: 1.65;
  margin: 0 0 1.5rem;
  font-style: italic;
}

.funnel__testimonial-author {
  display: flex;
  align-items: center;
  gap: 0.75rem;

  strong { display: block; color: colors.$OS-DARK; font-size: 0.88rem; font-weight: 700; }
  span { font-size: 0.78rem; color: #8A9BB5; }
}

.funnel__testimonial-avatar {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  i { color: #ffffff; font-size: 1.2rem; }
}

// ── Authority ────────────────────────────────────────────────────────────────
.funnel__authority {
  padding: 4rem 0;
  background: linear-gradient(135deg, #EEF4FF 0%, #F9FBFF 100%);
  border-top: 1px solid #E4EDF7;
  border-bottom: 1px solid #E4EDF7;
}

.funnel__authority-inner {
  display: flex;
  gap: 2.5rem;
  align-items: flex-start;
  @media (max-width: 640px) { flex-direction: column; align-items: center; }
}

.funnel__authority-photo-wrap { flex-shrink: 0; }

.funnel__authority-avatar {
  width: 112px;
  height: 112px;
  border-radius: 50%;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 4px solid #ffffff;
  box-shadow: 0 4px 20px rgba(0, 63, 125, 0.2);
  overflow: hidden;
}

.funnel__authority-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.funnel__authority-content { flex: 1; }

.funnel__authority-eyebrow {
  font-family: fonts.$font-interface;
  font-size: 0.76rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: colors.$OS-BLUE;
  margin: 0 0 0.35rem;
}

.funnel__authority-name {
  @include fonts.heading-font(800);
  font-size: 2rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.2rem;
  letter-spacing: -0.02em;
}

.funnel__authority-role {
  font-size: 0.88rem;
  color: #8A9BB5;
  margin: 0 0 1rem;
}

.funnel__authority-bio {
  font-size: 0.93rem;
  color: #3A4F6A;
  line-height: 1.65;
  margin: 0 0 1rem;
  strong { color: colors.$OS-DARK; font-weight: 700; }
}

.funnel__authority-creds {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;

  li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.86rem;
    color: #3A4F6A;
    i { color: colors.$OS-BLUE; font-size: 0.82rem; flex-shrink: 0; }
  }
}

// ── Hero subtitle ────────────────────────────────────────────────────────────
.funnel__hero-sub {
  font-size: 1.05rem;
  color: #3A4F6A;
  line-height: 1.65;
  margin: 0 0 1.5rem;
  max-width: 680px;
}

// ── Audience ─────────────────────────────────────────────────────────────────
.funnel__audience {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__audience-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__audience-col {
  padding: 1.75rem;
  border-radius: 16px;

  &--for {
    background: #EEF7F1;
    border: 1px solid rgba(#00B4A6, 0.2);
  }

  &--not {
    background: #FFF5F5;
    border: 1px solid rgba(#E63946, 0.15);
  }
}

.funnel__audience-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-family: fonts.$font-interface;
  font-size: 0.9rem;
  font-weight: 700;
  margin-bottom: 0.75rem;

  .funnel__audience-col--for & {
    color: colors.$PHB-AQUA;
    i { color: #00B4A6; }
  }

  .funnel__audience-col--not & {
    color: colors.$OS-RED;
    i { color: colors.$OS-RED; }
  }
}

.funnel__audience-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.45rem;

  li {
    font-size: 0.88rem;
    color: #3A4F6A;
    line-height: 1.5;
    padding-left: 1.25rem;
    position: relative;

    &::before {
      content: '•';
      position: absolute;
      left: 0;
      font-weight: 700;
    }
  }
}

// ── Problem extras ───────────────────────────────────────────────────────────
.funnel__problem-intro {
  font-size: 0.95rem;
  color: #4A5F7A;
  margin: -0.5rem 0 1.5rem;
  line-height: 1.5;
}

.funnel__problem-footer {
  font-size: 0.9rem;
  color: #4A5F7A;
  line-height: 1.6;
  margin: 1.25rem 0 0;
  padding: 1rem;
  background: #EEF4FF;
  border-left: 4px solid colors.$OS-BLUE;
  border-radius: 8px;
}

.funnel__cta-inline {
  margin-top: 1.5rem;
  display: flex;
  justify-content: center;
}

// ── 4 Dimensions ─────────────────────────────────────────────────────────────
.funnel__dimensions {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__dimensions-intro {
  font-size: 0.95rem;
  color: #4A5F7A;
  margin: -0.5rem 0 1.5rem;
  line-height: 1.5;
  font-style: italic;
}

.funnel__dimensions-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  @media (max-width: 640px) { grid-template-columns: 1fr; }
}

.funnel__dim-card {
  padding: 1.5rem;
  background: #F5F8FF;
  border: 1px solid #E4EDF7;
  border-radius: 14px;
  position: relative;
  overflow: hidden;
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 63, 125, 0.08);
  }
}

.funnel__dim-num {
  @include fonts.heading-font(800);
  font-size: 2.8rem;
  line-height: 1;
  color: rgba(colors.$OS-NAVY, 0.06);
  position: absolute;
  top: 0.5rem;
  right: 0.75rem;
  user-select: none;
}

.funnel__dim-title {
  @include fonts.heading-font(700);
  font-size: 1rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
  line-height: 1.3;
}

.funnel__dim-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0;
}

// ── 3 Pathways ───────────────────────────────────────────────────────────────
.funnel__pathways {
  padding: 4rem 0;
  background: #F5F8FF;
}

.funnel__pathways-intro {
  font-size: 0.95rem;
  color: #4A5F7A;
  margin: -0.5rem 0 1.5rem;
  line-height: 1.5;
}

.funnel__pathways-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  margin-bottom: 1.5rem;
  @media (max-width: 768px) { grid-template-columns: 1fr; }
}

.funnel__path-card {
  background: #ffffff;
  border: 1px solid #E4EDF7;
  border-radius: 14px;
  padding: 1.5rem;
  position: relative;
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 63, 125, 0.08);
  }

  &--phase {
    background: linear-gradient(135deg, colors.$OS-NAVY 0%, #0D2B4A 100%);
    border-color: colors.$OS-NAVY;
    display: flex;
    flex-direction: column;
    justify-content: center;
    grid-column: 1 / -1;

    .funnel__path-title { color: #ffffff; }
    .funnel__path-body { color: rgba(255, 255, 255, 0.7); }
  }
}

.funnel__path-badge {
  display: inline-flex;
  align-items: center;
  padding: 0.2rem 0.7rem;
  border-radius: 999px;
  background: rgba(255,255,255,0.12);
  border: 1px solid rgba(255,255,255,0.2);
  font-family: fonts.$font-interface;
  font-size: 0.7rem;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 0.08em;
  margin-bottom: 0.75rem;
  width: fit-content;
}

.funnel__path-icon {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 0.75rem;
  i { color: #ffffff; font-size: 1rem; }
}

.funnel__path-tag {
  @include fonts.interface-font(700);
  font-size: 0.65rem;
  letter-spacing: 2px;
  color: colors.$OS-BLUE;
  text-transform: uppercase;
  display: block;
  margin-bottom: 0.35rem;
}

.funnel__path-title {
  @include fonts.heading-font(700);
  font-size: 1rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__path-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0 0 0.75rem;
}

.funnel__path-tech {
  @include fonts.interface-font(600);
  font-size: 0.62rem;
  letter-spacing: 1.5px;
  color: rgba(colors.$OS-NAVY, 0.35);
  text-transform: uppercase;
}

// ── Case Studies ─────────────────────────────────────────────────────────────
.funnel__cases {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__cases-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.25rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__case-card {
  background: #F9FBFF;
  border: 1px solid #E4EDF7;
  border-radius: 16px;
  overflow: hidden;
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 63, 125, 0.08);
  }
}

.funnel__case-header {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  padding: 1rem 1.25rem;
  background: colors.$OS-NAVY;
  color: #ffffff;
  font-family: fonts.$font-interface;
  font-size: 0.88rem;
  font-weight: 700;

  i { font-size: 1rem; opacity: 0.7; }
}

.funnel__case-detail {
  padding: 1.25rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;

  strong {
    display: block;
    font-family: fonts.$font-interface;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 1px;
    color: colors.$OS-BLUE;
    text-transform: uppercase;
    margin-bottom: 0.2rem;
  }

  p {
    font-size: 0.84rem;
    color: #3A4F6A;
    line-height: 1.55;
    margin: 0;
  }
}

.funnel__case-report strong { color: #7A5EFF; }
.funnel__case-outcome strong { color: #00B4A6; }

// ── Benefits ─────────────────────────────────────────────────────────────────
.funnel__benefits {
  padding: 4rem 0;
  background: #F5F8FF;
}

.funnel__benefits-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__benefit-card {
  background: #ffffff;
  border: 1px solid #E4EDF7;
  border-radius: 16px;
  padding: 2rem 1.5rem;
  text-align: center;
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 63, 125, 0.08);
  }
}

.funnel__benefit-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  background: linear-gradient(135deg, colors.$OS-NAVY 0%, colors.$OS-BLUE 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 1rem;
  i { color: #ffffff; font-size: 1.2rem; }
}

.funnel__benefit-title {
  @include fonts.heading-font(700);
  font-size: 1.05rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__benefit-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0 0 0.75rem;
}

.funnel__benefit-tag {
  @include fonts.interface-font(600);
  font-size: 0.6rem;
  letter-spacing: 2px;
  color: rgba(colors.$OS-NAVY, 0.3);
  text-transform: uppercase;
}

// ── Differentiators ──────────────────────────────────────────────────────────
.funnel__diffs {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__diffs-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__diff-card {
  padding: 2rem 1.5rem;
  border: 1px solid #E4EDF7;
  border-radius: 16px;
  text-align: center;
  transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 63, 125, 0.08);
    border-color: colors.$OS-BLUE;
  }
}

.funnel__diff-stat {
  @include fonts.heading-font(800);
  font-size: 2.8rem;
  line-height: 1;
  color: colors.$OS-BLUE;
  margin-bottom: 0.75rem;
}

.funnel__diff-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 0.75rem;
  i { color: #ffffff; font-size: 1.2rem; }
}

.funnel__diff-title {
  @include fonts.heading-font(700);
  font-size: 1rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__diff-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0 0 0.75rem;
}

.funnel__diff-tag {
  @include fonts.interface-font(600);
  font-size: 0.62rem;
  letter-spacing: 2px;
  color: rgba(colors.$OS-NAVY, 0.3);
  text-transform: uppercase;
}

// ── CTA Final ────────────────────────────────────────────────────────────────
.funnel__cta-final {
  padding: 4.5rem 0;
  background: colors.$OS-NAVY;
  text-align: center;

  .funnel__section-label { color: rgba(#ffffff, 0.5); }

  .funnel__cta-btn {
    margin: 0 auto 1rem;
    background: colors.$OS-RED;
    box-shadow: 0 4px 24px rgba(204, 0, 0, 0.4);
    &:hover { background: #AA0000; }
  }

  .funnel__cta-sub { color: rgba(#ffffff, 0.5); }
}

.funnel__cta-final-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.8rem, 4vw, 2.8rem);
  color: #ffffff;
  margin: 0 0 0.75rem;
  letter-spacing: -0.025em;
}

.funnel__cta-final-sub {
  font-size: 0.97rem;
  color: rgba(#ffffff, 0.72);
  margin: 0 auto 2rem;
  max-width: 520px;
  line-height: 1.55;
}

// ── Footer ───────────────────────────────────────────────────────────────────
.funnel__footer {
  background: colors.$OS-DARK;
  padding: 2rem 1.5rem;
}

.funnel__footer-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  text-align: center;
}

.funnel__footer-logo {
  height: 30px;
  width: auto;
  filter: brightness(100);
  opacity: 0.6;
  object-fit: contain;
}

.funnel__footer-links {
  display: flex;
  gap: 1.5rem;

  a {
    font-size: 0.78rem;
    color: rgba(#ffffff, 0.45);
    text-decoration: none;
    transition: color 0.2s;
    &:hover { color: rgba(#ffffff, 0.85); }
  }
}

.funnel__footer-copy {
  font-size: 0.72rem;
  color: rgba(#ffffff, 0.28);
  margin: 0;
}
</style>
