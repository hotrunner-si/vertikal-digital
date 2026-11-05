<script setup>
import { computed, onMounted, onUnmounted, ref, watch } from 'vue'
import moonIcon from './assets/icons/moon.svg'
import sunIcon from './assets/icons/sun.svg'

const siteUrl = 'https://digitalni-visinci.netlify.app'
const language = ref(location.pathname.startsWith('/en') ? 'en' : 'sl')
const theme = ref(localStorage.getItem('theme') || (matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light'))
const activeProject = ref(null)
const activeImage = ref(null)
const text = {
  sl: { metaTitle: 'Digitalni višinci | Spletne strani in aplikacije', metaDescription: 'Izdelava preprostih, premišljenih spletnih strani, blogov in spletnih aplikacij po meri.', brand: 'Digitalni višinci', hero: ['Digitalni produkti,', 'ki povedo več.'], order: 'Naroči po meri', aboutTitle: 'Preproste spletne rešitve za jasne ideje.', about: ['Izdelujem spletne strani in spletne aplikacije. Spodaj si poglej ponudbo in primere končnih izdelkov. Stopi v kontakt, če tudi ti želiš nekaj svojega.', 'Cena storitve se prilagodi glede na velikost spletne strani ali aplikacije, število podstrani, število dodanih datotek in splošne zahtevnosti postavitve strani.'], products: 'Digitalni izdelki', portfolio: 'Portfolio', portfolioTitle: 'Že narejene spletne strani.', visit: 'Obišči spletno stran', closeProject: 'Zapri predstavitev projekta', closeImage: 'Zapri celozaslonski ogled', image: 'Primer', screenshot: 'Posnetek zaslona', openImage: 'Odpri v celozaslonskem načinu', dark: 'Vključi temni način', light: 'Vključi svetli način' },
  en: { metaTitle: 'Digitalni višinci | Websites and web apps', metaDescription: 'Thoughtful custom websites, blogs and web applications built for clear ideas.', brand: 'Digitalni višinci', hero: ['Digital products,', 'that say more.'], order: 'Request a custom site', aboutTitle: 'Simple web solutions for clear ideas.', about: ['I build websites and web applications. Browse the services and examples of finished work below, then get in touch if you have an idea of your own.', 'The price is tailored to the scope of the website or app, its number of pages, files and overall complexity.'], products: 'Digital products', portfolio: 'Portfolio', portfolioTitle: 'Selected web projects.', visit: 'Visit website', closeProject: 'Close project details', closeImage: 'Close full-screen preview', image: 'Example', screenshot: 'Screenshot', openImage: 'Open full-screen', dark: 'Enable dark mode', light: 'Enable light mode' }
}
const products = [
  { number: '01', images: ['/nu02.png', '/nu05.png', '/blog06.png', '/blog03.png'], sl: { title: 'Osebna spletna vizitka ali blog', description: ['Jasna in prepoznavna osebna predstavitev, predstavitev vašega dela, povezave do stikov ter prostor za vaše ideje in zgodbe.', 'Cena postavitve: 250 € - 850 €', 'Vzdrževanje: 9 €/mesec - 29 €/mesec'] }, en: { title: 'Personal website or blog', description: ['A clear, memorable presentation of you and your work, contact details, and a place for your ideas and stories.', 'Setup: €250–€850', 'Maintenance: €9/month–€29/month'] } },
  { number: '02', images: ['/mamut01.png', '/mamut02.png', '/mamut04.png', '/mamut05.png'], sl: { title: 'Predstavitvena stran', description: ['Učinkovita predstavitev projektov, apartmajev, produktov ali storitev.', 'Cena postavitve: 150 € - 1350 €', 'Vzdrževanje: 5 €/mesec - 49 €/mesec'] }, en: { title: 'Showcase website', description: ['An effective presentation for projects, apartments, products or services.', 'Setup: €150–€1,350', 'Maintenance: €5/month–€49/month'] } },
  { number: '03', images: ['/sk05.png', '/spomini05.png', '/sk08.png', '/spomini03.png'], sl: { title: 'Spletne aplikacije', description: ['Digitalna orodja po meri za preprostejše procese in boljšo uporabniško izkušnjo.', 'Cena postavitve: 550 € - 3550 €', 'Vzdrževanje: 39 €/mesec - 109 €/mesec'] }, en: { title: 'Web applications', description: ['Custom digital tools for simpler processes and a better user experience.', 'Setup: €550–€3,550', 'Maintenance: €39/month–€109/month'] } }
]
const projects = [
  { images: ['/blog01.png', '/blog02.png', '/blog03.png', '/blog04.png', '/blog05.png', '/blog06.png'], url: 'https://gorski-blog.netlify.app', sl: { title: 'Gorski kilometri', category: 'Tekaški blog', description: 'Blog, namenjen zgodbam, izkušnjam in vsebinam iz sveta gorskega teka.' }, en: { title: 'Gorski kilometri', category: 'Trail running blog', description: 'A blog for stories, experiences and content from the world of trail running.' } },
  { images: ['/spomini02.png', '/spomini03.png', '/spomini04.png', '/spomini05.png', '/spomini06.png', '/spomini%2007.png', '/spomini08.png', '/spomini09.png'], url: 'https://trail-memories.netlify.app', sl: { title: 'Maps and memories', category: 'Zbirka spominov', description: 'Spletna zbirka spominov, poti in trenutkov, zapisanih na zemljevidu.' }, en: { title: 'Maps and memories', category: 'A collection of memories', description: 'A web collection of memories, routes and moments recorded on a map.' } },
  { images: ['/sk01.png', '/sk02.png', '/sk03.png', '/sk04.png', '/sk05.png', '/sk06.png', '/sk07.png', '/sk08.png'], url: '', sl: { title: 'Aplikacija za pregled zaloge', category: 'Administracija', description: 'Administrativna spletna aplikacija za pregleden nadzor zaloge.' }, en: { title: 'Inventory overview application', category: 'Administration', description: 'An administrative web application for clear inventory control.' } },
  { images: ['/mamut01.png', '/mamut02.png', '/mamut03.png', '/mamut04.png', '/mamut05.png'], url: 'https://apartma-mamut.si', sl: { title: 'Apartma Mamut 1850', category: 'Vizitka nastanitve', description: 'Predstavitvena spletna stran za gorsko nastanitev Apartma Mamut 1850.' }, en: { title: 'Apartma Mamut 1850', category: 'Accommodation website', description: 'A showcase website for the Apartma Mamut 1850 mountain accommodation.' } },
  { images: ['/nu01.png', '/nu02.png', '/nu03.png', '/nu04.png', '/nu05.png'], url: 'https://nejc-ursic.netlify.app', sl: { title: 'Nejc Uršič', category: 'Osebna predstavitev', description: 'Osebna spletna predstavitev z izbranimi projekti, izkušnjami in načini stika.' }, en: { title: 'Nejc Uršič', category: 'Personal website', description: 'A personal website with selected projects, experience and contact options.' } }
]
const t = computed(() => text[language.value])
const localizedProducts = computed(() => products.map(item => Object.assign({}, item, item[language.value])))
const localizedProjects = computed(() => projects.map(item => Object.assign({}, item, item[language.value])))
function updatePage() {
  const canonical = siteUrl + (language.value === 'en' ? '/en/' : '/')
  document.documentElement.lang = language.value
  document.documentElement.dataset.theme = theme.value
  document.title = t.value.metaTitle
  const values = [['meta[name="description"]', t.value.metaDescription], ['meta[property="og:title"]', t.value.metaTitle], ['meta[property="og:description"]', t.value.metaDescription], ['link[rel="canonical"]', canonical]]
  values.forEach(([selector, value]) => document.querySelector(selector)?.setAttribute(selector.startsWith('link') ? 'href' : 'content', value))
}
function selectLanguage(next) { if (next !== language.value) { history.pushState({}, '', next === 'en' ? '/en/' : '/'); language.value = next } }
function toggleTheme() { theme.value = theme.value === 'dark' ? 'light' : 'dark' }
function syncLanguage() { language.value = location.pathname.startsWith('/en') ? 'en' : 'sl' }
watch([language, theme], () => { localStorage.setItem('theme', theme.value); updatePage() })
onMounted(() => { updatePage(); addEventListener('popstate', syncLanguage) })
onUnmounted(() => removeEventListener('popstate', syncLanguage))
</script>

<template>
  <main>
    <section class="hero" aria-labelledby="hero-title">
      <div class="hero-topline">
        <p class="eyebrow">{{ t.brand }}</p>
        <div class="controls">
          <div class="language-switch"><button type="button" :class="{ active: language === 'sl' }"
              @click="selectLanguage('sl')">SL</button><span>/</span><button type="button"
              :class="{ active: language === 'en' }" @click="selectLanguage('en')">EN</button></div><button
            class="theme-toggle" type="button" :aria-label="theme === 'dark' ? t.light : t.dark"
            @click="toggleTheme"><img :src="theme === 'dark' ? sunIcon : moonIcon" alt="" /></button>
        </div>
      </div>
      <h1 id="hero-title"><span>{{ t.hero[0] }}</span><br />{{ t.hero[1] }}</h1><a class="hero-link" href="#izdelki">{{
        t.order }} <span aria-hidden="true">↓</span></a>
    </section>
    <section class="about" aria-labelledby="about-title">
      <h2 id="about-title">{{ t.aboutTitle }}</h2>
      <p v-for="paragraph in t.about" :key="paragraph">{{ paragraph }}</p>
    </section>
    <section id="izdelki" class="products" :aria-label="t.products">
      <article v-for="product in localizedProducts" :key="product.number" class="product-card">
        <div class="product-images"><img v-for="(image, index) in product.images" :key="image" class="product-image"
            :src="image" :alt="t.image + ' ' + (index + 1) + ': ' + product.title" loading="lazy" decoding="async" />
        </div>
        <div class="product-content">
          <p class="product-number">{{ product.number }}</p>
          <div>
            <h2>{{ product.title }}</h2>
            <p v-for="paragraph in product.description" :key="paragraph">{{ paragraph }}</p>
          </div>
        </div>
      </article>
    </section>
    <section id="portfolio" class="portfolio" aria-labelledby="portfolio-title">
      <div class="portfolio-heading">
        <p class="eyebrow">{{ t.portfolio }}</p>
        <h2 id="portfolio-title">{{ t.portfolioTitle }}</h2>
      </div>
      <div class="portfolio-grid"><button v-for="project in localizedProjects" :key="project.title"
          class="portfolio-card" type="button" @click="activeProject = project"><img class="portfolio-image"
            :src="project.images[0]" :alt="t.image + ': ' + project.title" loading="lazy" decoding="async" /><span
            class="portfolio-card-meta"><span>{{ project.title }}</span><small>{{ project.category
              }}</small></span></button></div>
    </section>
  </main>
  <div v-if="activeProject" class="modal-backdrop" role="presentation" @click.self="activeProject = null">
    <section class="project-modal" role="dialog" aria-modal="true"><button class="modal-close" type="button"
        :aria-label="t.closeProject" @click="activeProject = null">×</button>
      <p class="eyebrow">{{ activeProject.category }}</p>
      <h2>{{ activeProject.title }}</h2>
      <p class="modal-description">{{ activeProject.description }}</p>
      <div class="screenshots"><button v-for="(screenshot, index) in activeProject.images" :key="screenshot"
          class="screenshot-button" type="button" :aria-label="t.openImage"
          @click="activeImage = { src: screenshot, alt: t.screenshot + ' ' + (index + 1) + ': ' + activeProject.title }"><img
            class="screenshot" :src="screenshot" :alt="t.screenshot + ' ' + (index + 1)" loading="lazy"
            decoding="async" /></button></div><a v-if="activeProject.url" class="project-link" :href="activeProject.url"
        target="_blank" rel="noopener noreferrer">{{ t.visit }} <span aria-hidden="true">↗</span></a>
    </section>
  </div>
  <div v-if="activeImage" class="image-lightbox" role="dialog" aria-modal="true" :aria-label="activeImage.alt"
    @click.self="activeImage = null"><button class="modal-close lightbox-close" type="button" :aria-label="t.closeImage"
      @click="activeImage = null">×</button><img :src="activeImage.src" :alt="activeImage.alt" /></div>
  <footer><a href="#">{{ t.brand }}</a>
    <p>© {{ new Date().getFullYear() }}</p>
  </footer>
</template>
