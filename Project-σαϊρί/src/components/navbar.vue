<template>  <div class="min-h-screen relative">    <!-- Animación de joyas flotantes -->
    <div class="jewels-animation fixed inset-0 pointer-events-none z-10 overflow-hidden">      <div v-for="i in 20" :key="`jewel-${i}`" 
           :class="`jewel jewel-${i % 15 + 1}`"
           class="absolute rounded-lg shadow-lg">
      </div>
      <!-- Emojis de corazones y elementos románticos -->      <div v-for="i in 15" :key="`heart-${i}`" 
           :class="`heart heart-${i % 10 + 1}`"
           class="absolute text-center">
           <span class="text-2xl sm:text-3xl">{{ heartEmojis[i % heartEmojis.length] }}</span>
      </div>
    </div>

    <!-- Contenedor principal con z-index -->
    <div class="relative z-1">
      <!-- Header y contenido existente -->
      <header class="fixed top-0 left-0 right-0 z-50 bg-white shadow-md">
        <nav class="container mx-auto px-4 py-2">
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <a href="#" class="flex items-center">
                <img src="../assets/logo.png" alt="Logo Joyería Tu Estilo" class="w-12 h-12 mr-2">
                <span class="text-title font-dancing">Joyería Tu Estilo</span>
              </a>
            </div>

            <!-- Menú de escritorio -->
            <div class="hidden md:flex items-center space-x-8">
              <a 
                v-for="item in menuItems"
                :key="item.id"
                :href="item.href"
                @click.prevent="scrollToSection(item.id)"
                class="relative group flex items-center space-x-2 px-4 py-2 rounded-lg transition-all duration-300"
                :class=" [
                  activeSection === item.id 
                    ? 'text-pink-500 bg-pink-50' 
                    : 'text-gray-700 hover:bg-gray-50'
                ]"
              >
                <component :is="item.icon" 
                          class="w-5 h-5 text-gray-400 group-hover:text-pink-500 transition-colors" 
                          :class="{ 'text-pink-500': activeSection === item.id }" />
                <span class="font-medium text-gray-700 group-hover:text-pink-500"
                      :class="{ 'text-pink-500': activeSection === item.id }">
                  {{ item.text }}
                </span>
              </a>
            </div>            <!-- Botón menú móvil mejorado con transición -->
            <button 
              @click="toggleMobileMenu" 
              class="md:hidden relative flex items-center space-x-1 px-3 py-2 rounded-full bg-gradient-to-r from-pink-500 to-purple-500 text-white hover:shadow-md transition-all duration-300 hover:scale-105 active:scale-95"
              aria-expanded="showMobileMenu"
              aria-controls="mobile-menu"
              aria-label="Menú principal"
            >
              <transition 
                mode="out-in"
                enter-active-class="transition-all duration-200 ease-out" 
                leave-active-class="transition-all duration-200 ease-in"
                enter-from-class="opacity-0 transform rotate-90" 
                leave-to-class="opacity-0 transform rotate-90"
              >
                <Menu v-if="!showMobileMenu" class="w-5 h-5" key="menu-icon"/>
                <X v-else class="w-5 h-5" key="close-icon"/>
              </transition>
              <span class="text-sm font-medium">{{ showMobileMenu ? 'Cerrar' : 'Menú' }}</span>
            </button>
          </div>
        </nav>
          <!-- Menú móvil con transiciones y animaciones -->
        <transition
          enter-active-class="transition ease-out duration-300"
          enter-from-class="opacity-0 transform -translate-y-10"
          enter-to-class="opacity-100 transform translate-y-0"
          leave-active-class="transition ease-in duration-200"
          leave-from-class="opacity-100 transform translate-y-0"
          leave-to-class="opacity-0 transform -translate-y-10"
        >          <div 
            v-if="showMobileMenu" 
            class="md:hidden fixed top-[64px] left-0 right-0 bottom-0 bg-white z-50 overflow-y-auto shadow-lg"
            id="mobile-menu"
          >
            <div class="p-4 space-y-2 mobile-menu-content">
              <!-- Encabezado del menú móvil -->
              <div class="text-center mb-4 pb-2 border-b border-gray-100">
                <h3 class="text-2xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-500 to-purple-500">
                  Menú de Navegación
                </h3>
              </div>
              
              <!-- Enlaces de navegación -->
              <a 
                v-for="(item, index) in menuItems" 
                :key="item.id"
                :href="item.href"
                @click.prevent="handleMobileNavClick($event, item.id)" 
                class="flex items-center space-x-3 p-4 rounded-lg transition-all duration-300"
                :class=" [
                  activeSection === item.id
                    ? 'bg-pink-50 text-pink-500'
                    : 'hover:bg-gray-50 text-gray-700'
                ]"
                :style="{animationDelay: `${index * 100}ms`}"
                :aria-label="`Ir a la sección ${item.text}`"
              >
                <div class="flex items-center justify-center w-10 h-10 rounded-full bg-white shadow-sm">
                  <component 
                    :is="item.icon"
                    class="w-5 h-5"
                    :class=" [
                      activeSection === item.id
                        ? 'text-pink-500'
                        : 'text-gray-500'
                    ]"
                  />
                </div>
                <span class="text-lg font-medium">{{ item.text }}</span>
                <ChevronRight class="w-5 h-5 text-gray-400 ml-auto" />
              </a>
              
              <!-- Información de contacto en el menú móvil -->
              <div class="mt-8 pt-6 border-t border-gray-100">
                <h3 class="text-lg font-medium text-gray-900 mb-4">Contacto Rápido</h3>
                <div class="space-y-3">
                  <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
                     class="flex items-center space-x-3 p-3 rounded-lg bg-green-50 hover:bg-green-100 transition-colors"
                     aria-label="Enviar mensaje por WhatsApp">
                    <MessageCircle class="w-5 h-5 text-green-500" />
                    <span class="text-green-700">Enviar WhatsApp</span>
                  </a>
                  <a :href="instagramUrl" target="_blank" rel="noopener noreferrer"
                     class="flex items-center space-x-3 p-3 rounded-lg bg-pink-50 hover:bg-pink-100 transition-colors"
                     aria-label="Visitar perfil de Instagram">
                    <Instagram class="w-5 h-5 text-pink-500" />
                    <span class="text-pink-700">Visitar Instagram</span>
                  </a>
                </div>
              </div>
              
              <!-- Botón para cerrar el menú -->
              <button 
                @click="toggleMobileMenu"
                class="mt-6 w-full p-3 rounded-lg bg-gray-100 hover:bg-gray-200 transition-colors flex items-center justify-center"
                aria-label="Cerrar menú"
              >
                <X class="w-5 h-5 text-gray-700 mr-2" />
                <span>Cerrar menú</span>
              </button>
            </div>
          </div>
        </transition>
      </header>

      <!-- Hero Section con Slider -->
      <section id="inicio" class="pt-24 pb-16 bg-gradient-to-br from-pink-50 via-pink-100/30 to-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <!-- Textos del hero -->
          <div class="text-center mb-12 md:mb-16 relative">
            <h2 class="animate-text text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-500 to-pink-400 mb-4 transform hover:scale-105 transition-transform duration-300">
              Encuentra La Joya Perfecta
            </h2>
            
            <!-- Texto animado con cambios de color -->
            <div class="animate-text text-lg sm:text-xl md:text-2xl mt-6 text-gray-600 max-w-3xl mx-auto leading-relaxed">
              <p class="mb-2 changing-text">Joyería, carteras, relojes, cargadores, audífonos y más</p>
              <p class="animated-text">Todo lo que necesitas para complementar tu estilo único</p>
              
              <!-- Palabras rotativas -->
              <div class="rotating-words mt-4">
                <span class="word-rotation">
                  <span>✨ Elegancia</span>
                  <span>💎 Estilo</span>
                  <span>🌸 Glamour</span>
                  <span>💫 Belleza</span>
                  <span>🌟 Distinción</span>
                  <span>💖 Originalidad</span>

                </span>
              </div>
            </div>
            
            <div class="absolute -top-10 left-1/2 transform -translate-x-1/2 w-64 h-64 bg-pink-200 rounded-full filter blur-3xl opacity-20 animate-pulse"></div>
          </div>

          <!-- Slider adaptativo -->
          <div class="relative w-full h-[300px] sm:h-[400px] md:h-[500px] lg:h-[600px] overflow-hidden rounded-2xl group shadow-2xl">
            <div class="absolute w-full h-full flex transition-transform duration-1000 ease-out"
                 :style="{ transform: `translateX(-${currentSlide * 100}%)` }">
              <div v-for="(slide, index) in slides" 
                   :key="index" 
                   class="w-full h-full flex-shrink-0 relative">
                <img :src="slide.image" 
                     :alt="slide.title"
                     class="w-full h-full object-cover transition-all duration-1000"
                     :class="{ 'scale-110 blur-sm': currentSlide !== index, 'scale-100': currentSlide === index }"
                     loading="lazy">
                <div class="absolute inset-0 bg-gradient-to-t from-pink-900/80 via-pink-500/20 to-transparent opacity-70 transition-opacity duration-500"></div>
                <div class="absolute bottom-0 left-0 right-0 p-4 sm:p-6 md:p-8 transform transition-all duration-700"
                     :class="[currentSlide === index ? 'translate-y-0 opacity-100' : 'translate-y-10 opacity-0']">
                  <h3 class="text-white text-2xl sm:text-3xl md:text-4xl font-bold mb-2 md:mb-4 tracking-tight">{{ slide.title }}</h3>
                  <p class="text-white/90 text-base sm:text-lg md:text-xl max-w-2xl leading-relaxed font-light">{{ slide.description }}</p>
                </div>
              </div>
            </div>

            <!-- Controles del slider -->
            <button @click="prevSlide"
                    @mouseenter="stopAutoplay"
                    @mouseleave="startAutoplay"
                    class="absolute left-2 sm:left-4 top-1/2 -translate-y-1/2 p-2 sm:p-3 md:p-4 rounded-full bg-pink-500/20 backdrop-blur-md hover:bg-pink-500/40 transition-all duration-300 opacity-0 group-hover:opacity-100 transform hover:scale-110">
              <ChevronLeft class="w-6 h-6 sm:w-7 sm:h-7 md:w-8 md:h-8 text-white" />
            </button>
            <button @click="nextSlide"
                    @mouseenter="stopAutoplay"
                    @mouseleave="startAutoplay"
                    class="absolute right-2 sm:right-4 top-1/2 -translate-y-1/2 p-2 sm:p-3 md:p-4 rounded-full bg-pink-500/20 backdrop-blur-md hover:bg-pink-500/40 transition-all duration-300 opacity-0 group-hover:opacity-100 transform hover:scale-110">
              <ChevronRight class="w-6 h-6 sm:w-7 sm:h-7 md:w-8 md:h-8 text-white" />
            </button>

            <!-- Indicadores -->
            <div class="absolute -bottom-4 left-0 right-0 flex justify-center space-x-2 md:space-x-3 z-10">
              <button v-for="(_, index) in slides" 
                      :key="index"
                      @click="setSlide(index)"
                      @mouseenter="stopAutoplay"
                      @mouseleave="startAutoplay"
                      class="w-12 sm:w-16 md:w-20 h-1.5 rounded-full transition-all duration-500 transform hover:scale-110"
                      :class="[currentSlide === index ? 'bg-pink-500 w-16 sm:w-20 md:w-24' : 'bg-white/50 hover:bg-white']">
              </button>
            </div>
          </div>
        </div>
      </section>
      
      <!-- Sección de Posts de Instagram -->
      <section class="py-16 bg-gradient-to-br from-pink-50 to-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="text-center mb-12">
            <h2 class="text-3xl md:text-4xl font-bold mb-4">Nuestros Productos</h2>
            <p class="text-gray-600 max-w-3xl mx-auto">
              Mantente al día con nuestras últimas novedades y productos En nuestra cuenta de Instagram.
            </p>
          </div>

          <!-- Grid de Posts de Instagram -->
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
            <div v-for="(post, index) in instagramPosts" :key="index" 
                 class="instagram-post-container bg-white rounded-xl shadow-md overflow-hidden">
              <blockquote 
                class="instagram-media" 
                :data-instgrm-captioned="true"
                :data-instgrm-permalink="post.url"
                data-instgrm-version="14"
                style="background:#FFF; border:0; border-radius:15px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%;"
              >
                <div style="padding:16px;"></div>
              </blockquote>
            </div>
          </div>

          <!-- Botón de Seguir en Instagram -->
          <div class="text-center mt-12">
            <a 
              href="https://www.instagram.com/joyeria_tu_estilo_/"
              target="_blank"
              rel="noopener noreferrer"
              class="inline-flex items-center px-6 py-3 bg-gradient-to-r from-pink-500 to-purple-500 text-white font-medium rounded-lg shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1"
            >
              <Instagram class="w-5 h-5 mr-2" />
              Ver mas en Instagram
            </a>
          </div>
        </div>
      </section>

      <!-- Sección de Ubicación -->
      <section id="ubicacion" class="py-16 bg-gradient-to-br from-pink-50 to-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="text-center mb-12">
            <h2 class="text-3xl md:text-4xl font-bold mb-4">Puntos de Entrega</h2>
            <p class="text-gray-600 max-w-3xl mx-auto">
              Elige el lugar más conveniente para recibir tus productos
            </p>
          </div>          <!-- Mapa principal de ubicación -->
          <div class="mb-10 bg-white rounded-xl shadow-lg overflow-hidden">
            <h3 class="text-xl font-bold p-4 bg-gradient-to-r from-pink-500 to-purple-500 text-white flex items-center">
              <MapPin class="w-5 h-5 mr-2" />
              Nuestra Ubicación Principal - Frente a Piscinas El Capulín
            </h3>
            
            <div class="p-4 bg-pink-50">
              <p class="text-gray-700 mb-3">
                <span class="font-semibold">Moncagua, San Miguel</span> - <span class="text-pink-600">Frente a las piscinas El Capulín</span>
                <br>
                <small class="text-gray-500">Horario de atención: Lunes a Sábado, 9:00 AM - 6:00 PM</small>
              </p>
              
              <!-- Instrucciones para llegar -->
              <div class="p-3 bg-white rounded-lg mb-3 border border-pink-100">
                <h4 class="text-base font-semibold text-gray-800 mb-2 flex items-center">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7" />
                  </svg>
                  ¿Cómo llegar?
                </h4>
                <p class="text-sm text-gray-600 mb-2">
                  Desde San Miguel, toma la carretera hacia Moncagua. Al llegar al pueblo, sigue por la calle principal hasta identificar "Las piscinas El Capulín".
                </p>                <div class="flex flex-wrap gap-2 mt-3">
                  <a 
                    :href="ubicacionPrincipalMapUrl"
                    target="_blank"
                    class="text-sm text-pink-600 hover:text-pink-800 inline-flex items-center bg-pink-50 hover:bg-pink-100 px-3 py-1.5 rounded-full transition-colors"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                    </svg>
                    Ver en Google Maps
                  </a>
                  <a 
                    href="https://wa.me/+50372011707?text=Hola,%20me%20gustaría%20consultar%20cómo%20llegar%20a%20su%20local"
                    target="_blank"
                    class="text-sm text-green-600 hover:text-green-800 inline-flex items-center bg-green-50 hover:bg-green-100 px-3 py-1.5 rounded-full transition-colors"
                  >
                    <MessageCircle class="w-4 h-4 mr-1.5" />
                    Consultar cómo llegar
                  </a>
                  <a 
                    href="tel:+50372011707"
                    class="text-sm text-blue-600 hover:text-blue-800 inline-flex items-center bg-blue-50 hover:bg-blue-100 px-3 py-1.5 rounded-full transition-colors"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
                    </svg>
                    Llamar para indicaciones
                  </a>
                </div>
              </div>
            </div>              <!-- Mapa mejorado con Google Maps -->
            <div class="w-full h-[450px] relative overflow-hidden rounded-b-xl shadow-lg border border-gray-200">
              <!-- Contenedor del mapa -->
              <iframe 
                :src="ubicacionIframeUrl" 
                class="w-full h-full border-0" 
                allowfullscreen="" 
                loading="lazy" 
                referrerpolicy="no-referrer-when-downgrade"
                title="Ubicación de Joyería Tu Estilo, Moncagua, San Miguel"
              ></iframe>
              
              <!-- Panel de controles del mapa -->
              <div class="absolute top-4 right-4 flex flex-col gap-2">
                <!-- Botones de zoom -->
                <button class="w-8 h-8 bg-white rounded-md shadow-md flex items-center justify-center hover:bg-gray-100 transition-colors">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-700" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" />
                  </svg>
                </button>
                <button class="w-8 h-8 bg-white rounded-md shadow-md flex items-center justify-center hover:bg-gray-100 transition-colors">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-700" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1z" clip-rule="evenodd" />
                  </svg>
                </button>
              </div>
              
              <!-- Información and controles inferiores -->
              <div class="absolute left-0 right-0 bottom-0 bg-gradient-to-t from-black/60 to-transparent pt-16 px-4 pb-4">
                <div class="flex flex-wrap items-end justify-between gap-2">
                  <!-- Badge de horario -->
                  <div class="bg-white/90 backdrop-blur-sm px-3 py-1.5 rounded-lg shadow-md">
                    <p class="text-xs font-medium text-pink-600 flex items-center">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                      </svg>
                      Lunes a Sábado, 9:00 AM - 6:00 PM
                    </p>
                  </div>
                  
                  <!-- Botones de acción -->
                  <div class="flex gap-2">
                    <a 
                      :href="ubicacionPrincipalMapUrl" 
                      target="_blank"
                      class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1.5 rounded-lg shadow-md transition-colors flex items-center text-sm"
                    >
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                      </svg>
                      Abrir en Google Maps
                    </a>
                    <a 
                      :href="whatsappUrl + '?text=Hola,%20quisiera%20visitar%20su%20local,%20¿pueden%20darme%20más%20indicaciones?'" 
                      target="_blank"
                      class="bg-green-500 hover:bg-green-600 text-white px-3 py-1.5 rounded-lg shadow-md transition-colors flex items-center text-sm"
                    >
                      <MessageCircle class="w-4 h-4 mr-1.5" />
                      Consultar cómo llegar
                    </a>
                  </div>
                </div>
              </div>

              <!-- Indicador de ubicación personalizado -->
              <div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 pointer-events-none">
                <div class="relative">
                  <div class="w-6 h-6 rounded-full bg-pink-500 border-2 border-white shadow-lg flex items-center justify-center animate-pulse">
                    <div class="w-2 h-2 rounded-full bg-white"></div>
                  </div>
                  <div class="absolute -bottom-1 left-1/2 transform -translate-x-1/2 translate-y-full">
                    <div class="bg-white px-3 py-1 rounded shadow-md text-xs font-medium text-pink-600 whitespace-nowrap">
                      Piscinas El Capulín
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>          <!-- Título de puntos de entrega con estilo moderno -->
          <div class="mb-10 text-center">
            <h3 class="text-2xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-500 to-purple-500 inline-block">
              Puntos de Entrega Disponibles
            </h3>
            <div class="h-1 w-24 bg-gradient-to-r from-pink-500 to-purple-500 rounded-full mx-auto mt-2 mb-8"></div>
          </div>
          
          <!-- Grid de puntos de entrega - ahora con 4 ubicaciones -->
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 lg:gap-6 max-w-6xl mx-auto">
            <!-- 1. Punto de entrega: Parque Central de Moncagua -->
            <div class="bg-white rounded-xl shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100 overflow-hidden">
              <div class="bg-gradient-to-r from-pink-400 to-pink-500 p-2 text-center text-white">
                <h3 class="text-base font-bold">1. Parque Central de Moncagua</h3>
              </div>
              
              <div class="p-3 flex flex-col items-center">
                <div class="bg-pink-100 rounded-full w-10 h-10 flex items-center justify-center mb-2">
                  <MapPin class="w-5 h-5 text-pink-500" />
                </div>
                <p class="text-xs text-gray-500 text-center mb-3">Punto de encuentro principal en el centro de la ciudad</p>
                
                <a 
                  href="https://wa.me/+50372011707?text=Hola,%20quisiera%20coordinar%20una%20entrega%20en%20el%20Parque%20Central%20de%20Moncagua" 
                  target="_blank" 
                  class="w-full py-2 text-center text-white bg-green-500 hover:bg-green-600 rounded-lg transition-colors text-sm"
                >
                  Coordinar entrega
                </a>
              </div>
            </div>
            
            <!-- 2. Punto de entrega: Parque Central de San Miguel -->
            <div class="bg-white rounded-xl shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100 overflow-hidden">
              <div class="bg-gradient-to-r from-pink-400 to-pink-500 p-2 text-center text-white">
                <h3 class="text-base font-bold">2. Parque Central de San Miguel</h3>
              </div>
              
              <div class="p-3 flex flex-col items-center">
                <div class="bg-pink-100 rounded-full w-10 h-10 flex items-center justify-center mb-2">
                  <MapPin class="w-5 h-5 text-pink-500" />
                </div>
                <p class="text-xs text-gray-500 text-center mb-3">Punto céntrico en la ciudad de San Miguel</p>
                
                <a 
                  href="https://wa.me/+50372011707?text=Hola,%20quisiera%20coordinar%20una%20entrega%20en%20el%20Parque%20Central%20de%20San%20Miguel" 
                  target="_blank" 
                  class="w-full py-2 text-center text-white bg-green-500 hover:bg-green-600 rounded-lg transition-colors text-sm"
                >
                  Coordinar entrega
                </a>
              </div>
            </div>
            
            <!-- 3. Punto de entrega: Plaza El Encuentro -->
            <div class="bg-white rounded-xl shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100 overflow-hidden">
              <div class="bg-gradient-to-r from-pink-400 to-pink-500 p-2 text-center text-white">
                <h3 class="text-base font-bold">3. Plaza El Encuentro, San Miguel</h3>
              </div>
              
              <div class="p-3 flex flex-col items-center">
                <div class="bg-pink-100 rounded-full w-10 h-10 flex items-center justify-center mb-2">
                  <MapPin class="w-5 h-5 text-pink-500" />
                </div>
                <p class="text-xs text-gray-500 text-center mb-3">Área comercial con fácil acceso</p>
                
                <a 
                  href="https://wa.me/+50372011707?text=Hola,%20quisiera%20coordinar%20una%20entrega%20en%20Plaza%20El%20Encuentro,%20San%20Miguel" 
                  target="_blank" 
                  class="w-full py-2 text-center text-white bg-green-500 hover:bg-green-600 rounded-lg transition-colors text-sm"
                >
                  Coordinar entrega
                </a>
              </div>
            </div>
            
            <!-- 4. Punto de entrega: Metrocentro San Miguel -->
            <div class="bg-white rounded-xl shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100 overflow-hidden">
              <div class="bg-gradient-to-r from-pink-400 to-pink-500 p-2 text-center text-white">
                <h3 class="text-base font-bold">4. Metrocentro San Miguel</h3>
              </div>
              
              <div class="p-3 flex flex-col items-center">
                <div class="bg-pink-100 rounded-full w-10 h-10 flex items-center justify-center mb-2">
                  <MapPin class="w-5 h-5 text-pink-500" />
                </div>
                <p class="text-xs text-gray-500 text-center mb-3">Centro comercial principal de la zona</p>
                
                <a 
                  href="https://wa.me/+50372011707?text=Hola,%20quisiera%20coordinar%20una%20entrega%20en%20Metrocentro%20San%20Miguel" 
                  target="_blank" 
                  class="w-full py-2 text-center text-white bg-green-500 hover:bg-green-600 rounded-lg transition-colors text-sm"
                >
                  Coordinar entrega
                </a>
              </div>
            </div>
          </div>
            <!-- Ubicación principal con estilo mejorado -->
          <div class="mt-12 max-w-4xl mx-auto">
            <div class="bg-white rounded-xl shadow-lg overflow-hidden border border-pink-100">
              <div class="bg-gradient-to-r from-pink-500 to-purple-500 p-4 text-white relative">
                <div class="flex items-start">
                  <div class="bg-white/20 rounded-full p-2 mr-3">
                    <MapPin class="w-6 h-6" />
                  </div>
                  <div>
                    <h3 class="text-lg font-bold">Nuestra Ubicación Principal</h3>
                    <p class="text-sm text-white/90">Frente a las piscinas El Capulín, Moncagua, San Miguel</p>
                  </div>
                </div>
                <div class="absolute -bottom-4 right-4">
                  <div class="bg-pink-100 h-8 w-8 rounded-full flex items-center justify-center shadow-lg animate-bounce">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
                    </svg>
                  </div>
                </div>
              </div>
              
              <div class="p-6">
                <div class="flex items-center mb-4 bg-pink-50 p-3 rounded-lg">
                  <div class="bg-pink-100 rounded-full w-10 h-10 flex items-center justify-center mr-3 flex-shrink-0">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                  </div>
                  <div>
                    <h4 class="font-semibold text-gray-800">Horario de atención</h4>
                    <p class="text-sm text-gray-600">Lunes a Sábado, 9:00 AM - 6:00 PM</p>
                  </div>
                </div>
                
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                  <a 
                    :href="ubicacionPrincipalMapUrl"
                    target="_blank" 
                    class="py-3 text-center text-white bg-blue-500 hover:bg-blue-600 rounded-lg transition-all duration-300 font-medium flex items-center justify-center shadow-md hover:shadow-lg transform hover:-translate-y-1"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                    </svg>
                    Ver en Google Maps
                  </a>
                  
                  <a 
                    href="https://wa.me/+50372011707?text=Hola,%20quisiera%20coordinar%20una%20entrega%20en%20su%20local%20frente%20a%20piscinas%20El%20Capulín" 
                    target="_blank" 
                    class="py-3 text-center text-white bg-green-500 hover:bg-green-600 rounded-lg transition-all duration-300 font-medium flex items-center justify-center shadow-md hover:shadow-lg transform hover:-translate-y-1"
                  >
                    <MessageCircle class="w-5 h-5 mr-2" />
                    Coordinar entrega
                  </a>
                </div>
              </div>
            </div>
          </div>          <!-- Información sobre proceso de entrega -->
          <div class="mt-16 bg-gradient-to-r from-pink-50 to-pink-100/50 p-6 rounded-xl max-w-3xl mx-auto shadow-lg">
            <div class="text-center mb-6">
              <h3 class="text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-600 to-purple-600">
                ¿Cómo coordinar tu entrega?
              </h3>
              <div class="h-1 w-16 bg-gradient-to-r from-pink-500 to-purple-500 rounded-full mx-auto mt-2"></div>
            </div>
            
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 mb-6">
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">1</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Selecciona tus productos</h4>
                <p class="text-xs text-gray-600">
                  Elige los productos que deseas comprar de nuestro catálogo
                </p>
              </div>
              
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">2</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Coordina por WhatsApp</h4>
                <p class="text-xs text-gray-600">
                  Contáctanos para acordar día, hora y punto de entrega
                </p>
              </div>
              
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">3</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Recibe tu producto</h4>
                <p class="text-xs text-gray-600">
                  Nos encontramos en el punto acordado y recibes tu compra
                </p>
              </div>
            </div>
            
            <div class="text-center">
              <a 
                href="https://wa.me/+50372011707?text=Hola,%20me%20gustaría%20coordinar%20una%20entrega"
                target="_blank"
                class="inline-flex items-center px-6 py-3 rounded-full bg-green-500 text-white hover:bg-green-600 transition-all text-center shadow-md"
              >
                <MessageCircle class="w-5 h-5 mr-2" />
                <span class="font-medium">Coordinar mi entrega</span>
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- Sección de Contacto -->
    

      <!-- Sección de Contacto -->
      <section id="contacto" class="py-16 bg-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="grid grid-cols-1 lg:grid-cols-2 gap-12 items-center">
            <div>
              <h2 class="text-3xl md:text-4xl font-bold mb-6 text-gray-800">
                Contáctanos
              </h2>
              <p class="text-lg text-gray-600 mb-8">
                Estamos disponibles para responder tus preguntas y ayudarte a encontrar la joya perfecta para cada ocasión.
              </p>
              
              <div class="space-y-5">
                <div class="flex items-center bg-white shadow-sm hover:shadow-md rounded-xl p-5 border border-gray-100 transition-all duration-300">
                  <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center mr-4">
                    <MessageCircle class="w-6 h-6 text-green-600" />
                  </div>
                  <div>
                    <h3 class="font-medium text-gray-800">WhatsApp</h3>
                    <a href="tel:+50372011707" class="text-green-600 hover:text-green-700">+503 7201-1707</a>
                    <p class="text-sm text-gray-500 mt-1">Atención de lunes a sábado de 9 AM a 7 PM</p>
                  </div>
                </div>
                
                <div class="flex items-center bg-white shadow-sm hover:shadow-md rounded-xl p-5 border border-gray-100 transition-all duration-300">
                  <div class="w-12 h-12 bg-pink-100 rounded-full flex items-center justify-center mr-4">
                    <Instagram class="w-6 h-6 text-pink-600" />
                  </div>
                  <div>
                    <h3 class="font-medium text-gray-800">Instagram</h3>
                    <a href="https://www.instagram.com/joyeria_tu_estilo_/" target="_blank" class="text-pink-600 hover:text-pink-700">@joyeria_tu_estilo_</a>
                    <p class="text-sm text-gray-500 mt-1">Síguenos para ver nuestras últimas creaciones</p>
                  </div>
                </div>
                
                <div class="flex items-center bg-white shadow-sm hover:shadow-md rounded-xl p-5 border border-gray-100 transition-all duration-300">
                  <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mr-4">
                    <MapPin class="w-6 h-6 text-blue-600" />
                  </div>
                  <div>
                    <h3 class="font-medium text-gray-800">Ubicaciones</h3>
                    <p class="text-blue-600">San Miguel & Moncagua, El Salvador</p>
                    <p class="text-sm text-gray-500 mt-1">Entregas en los puntos indicados</p>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Mapa del punto de entrega en el parque de Moncagua -->
            <div class="bg-white p-6 md:p-8 rounded-2xl shadow-lg border border-gray-100">
              <h3 class="text-2xl font-bold mb-6 text-gray-800 flex items-center">
                <MapPin class="w-6 h-6 mr-2 text-pink-500" />
                Punto de Entrega Principal
              </h3>
              
              <div class="mb-6 bg-pink-50 p-4 rounded-lg">
                <h4 class="font-bold text-lg mb-2">Parque Central de Moncagua</h4>
                <p class="text-gray-600 mb-3">Nuestro punto principal de entrega es el Parque Central de Moncagua, un lugar céntrico y de fácil acceso para todos nuestros clientes.</p>
                <div class="flex items-center space-x-2 text-pink-600">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                  <span>Horario de entregas: Lunes a Sábado, 8:00 AM - 4:00 PM</span>
                </div>
              </div>
                <div class="rounded-xl overflow-hidden mb-6 border border-gray-200 bg-gray-100 h-[350px] relative">
                <div class="absolute inset-0 flex flex-col items-center justify-center p-4 text-center">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-pink-500 mb-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                  </svg>
                  <h3 class="text-xl font-medium mb-2">Parque Central de Moncagua</h3>
                  <p class="text-gray-600 mb-4">Punto de entrega principal</p>                  <a 
                    :href="parqueCentralMoncaguaMapUrl" 
                    target="_blank" 
                    class="px-4 py-2 bg-pink-500 text-white rounded-lg hover:bg-pink-600 transition-all"
                  >
                    Ver en Google Maps
                  </a>
                </div>
              </div>
              
              <div class="flex flex-wrap gap-3 justify-center">                <a 
                  :href="whatsappUrl"
                  target="_blank"
                  class="inline-flex items-center px-4 py-2 bg-green-500 hover:bg-green-600 text-white font-medium rounded-lg shadow-sm hover:shadow-md transition-colors"
                >
                  <MessageCircle class="w-5 h-5 mr-2" />
                  Coordinar entrega
                </a>                <a 
                  :href="ubicacionPrincipalMapUrl" 
                  target="_blank"
                  class="inline-flex items-center px-4 py-2 bg-blue-500 hover:bg-blue-600 text-white font-medium rounded-lg shadow-sm hover:shadow-md transition-colors"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7" />
                  </svg>
                  Ver en Google Maps
                </a>
              </div>
            </div>
          </div>          <!-- Información sobre proceso de entrega -->
          <div class="mt-16 bg-gradient-to-r from-pink-50 to-pink-100/50 p-6 rounded-xl max-w-3xl mx-auto shadow-lg">
            <div class="text-center mb-6">
              <h3 class="text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-600 to-purple-600">
                ¿Cómo coordinar tu entrega?
              </h3>
              <div class="h-1 w-16 bg-gradient-to-r from-pink-500 to-purple-500 rounded-full mx-auto mt-2"></div>
            </div>
            
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 mb-6">
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">1</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Selecciona tus productos</h4>
                <p class="text-xs text-gray-600">
                  Elige los productos que deseas comprar de nuestro catálogo
                </p>
              </div>
              
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">2</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Coordina por WhatsApp</h4>
                <p class="text-xs text-gray-600">
                  Contáctanos para acordar día, hora y punto de entrega
                </p>
              </div>
              
              <div class="bg-white p-4 rounded-xl shadow-sm text-center">
                <div class="bg-pink-100 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-3">
                  <span class="text-pink-500 font-bold">3</span>
                </div>
                <h4 class="text-sm font-semibold mb-2 text-gray-800">Recibe tu producto</h4>
                <p class="text-xs text-gray-600">
                  Nos encontramos en el punto acordado y recibes tu compra
                </p>
              </div>
            </div>
            
            <div class="text-center">
              <a 
                href="https://wa.me/+50372011707?text=Hola,%20me%20gustaría%20coordinar%20una%20entrega"
                target="_blank"
                class="inline-flex items-center px-6 py-3 rounded-full bg-green-500 text-white hover:bg-green-600 transition-all text-center shadow-md"
              >
                <MessageCircle class="w-5 h-5 mr-2" />
                <span class="font-medium">Coordinar mi entrega</span>
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- Footer moderno y elegante -->
      <footer class="bg-gradient-to-b from-white to-pink-50 pt-16 pb-8 relative overflow-hidden">
        <!-- Decoración de fondo -->
        <div class="absolute top-0 left-0 right-0 h-1 bg-gradient-to-r from-pink-300 via-purple-400 to-pink-300"></div>
        <div class="absolute inset-0 overflow-hidden pointer-events-none opacity-10">
          <div class="absolute -top-20 -right-20 w-64 h-64 rounded-full bg-gradient-to-br from-pink-300 to-purple-400 blur-3xl"></div>
          <div class="absolute -bottom-32 -left-32 w-96 h-96 rounded-full bg-gradient-to-tr from-pink-400 to-purple-300 blur-3xl"></div>
        </div>
        
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10">
          <!-- Logo y enlaces principales -->
          <div class="flex flex-col lg:flex-row justify-between items-start lg:items-center mb-12 pb-12 border-b border-pink-100">
            <div class="flex items-center mb-8 lg:mb-0">
              <div class="w-16 h-16 bg-gradient-to-br from-pink-500 to-purple-500 rounded-full shadow-lg flex items-center justify-center mr-4 p-1">
                <div class="bg-white rounded-full w-full h-full flex items-center justify-center">
                  <span class="text-2xl">💖</span>
                </div>
              </div>
              <div>
                <h2 class="text-3xl font-dancing font-bold bg-gradient-to-r from-pink-500 to-purple-500 bg-clip-text text-transparent mb-1">
                  Joyería Tu Estilo
                </h2>
                <p class="text-gray-500 text-sm">Belleza que perdura en el tiempo</p>
              </div>
            </div>
            
            <!-- Botones de redes sociales -->
            <div class="flex space-x-3">
              <a :href="instagramUrl" target="_blank" rel="noopener noreferrer"
                 class="w-10 h-10 rounded-full bg-gradient-to-br from-purple-500 to-pink-500 flex items-center justify-center shadow-md hover:shadow-lg transform hover:-translate-y-1 transition-all duration-300">
                <Instagram class="w-5 h-5 text-white" />
              </a>
              <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
                 class="w-10 h-10 rounded-full bg-gradient-to-br from-green-500 to-green-400 flex items-center justify-center shadow-md hover:shadow-lg transform hover:-translate-y-1 transition-all duration-300">
                <MessageCircle class="w-5 h-5 text-white" />
              </a>
            </div>
          </div>
          
          <!-- Secciones del footer -->
          <div class="grid grid-cols-1 md:grid-cols-3 gap-8 lg:gap-16">
            <!-- Sobre nosotros -->
            <div>
              <h3 class="text-lg font-bold text-gray-800 mb-4 flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                Sobre Nosotros
              </h3>
              <p class="text-gray-600 leading-relaxed">
                Ofrecemos joyas únicas y especiales para cada ocasión. Encuentra la pieza perfecta que refleje tu estilo y personalidad, ideal para ti o para regalar a alguien especial.
              </p>
              <div class="mt-4 bg-white p-3 rounded-lg shadow-sm border border-pink-100">
                <p class="text-sm text-gray-700 italic">
                  "La belleza está en los detalles, y nuestras joyas son cuidadosamente seleccionadas para realzar tu belleza natural"
                </p>
              </div>
            </div>
            
            <!-- Enlaces rápidos con iconos -->
            <div>
              <h3 class="text-lg font-bold text-gray-800 mb-4 flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.828 10.172a4 4 0 00-5.656 0l-4 4a4 4 0 105.656 5.656l1.102-1.101m-.758-4.899a4 4 0 005.656 0l4-4a4 4 0 00-5.656-5.656l-1.1 1.1" />
                </svg>
                Enlaces Rápidos
              </h3>
              <ul class="space-y-3">
                <li>
                  <a href="#inicio" @click.prevent="scrollToSection('inicio')" 
                     class="flex items-center text-gray-600 hover:text-pink-500 transition-colors group">
                    <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-3 group-hover:bg-pink-200 transition-colors">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6" />
                      </svg>
                    </div>
                    Inicio
                  </a>
                </li>
                <li>
                  <a href="#productos" @click.prevent="scrollToSection('productos')" 
                     class="flex items-center text-gray-600 hover:text-pink-500 transition-colors group">
                    <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-3 group-hover:bg-pink-200 transition-colors">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z" />
                      </svg>
                    </div>
                    Productos
                  </a>
                </li>
                <li>
                  <a href="#ubicacion" @click.prevent="scrollToSection('ubicacion')" 
                     class="flex items-center text-gray-600 hover:text-pink-500 transition-colors group">
                    <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-3 group-hover:bg-pink-200 transition-colors">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                      </svg>
                    </div>
                    Ubicación
                  </a>
                </li>
                <li>
                  <a href="#contacto" @click.prevent="scrollToSection('contacto')" 
                     class="flex items-center text-gray-600 hover:text-pink-500 transition-colors group">
                    <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-3 group-hover:bg-pink-200 transition-colors">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 012 2z" />
                      </svg>
                    </div>
                    Contacto
                  </a>
                </li>
              </ul>
            </div>
            
            <!-- Información de contacto -->
            <div>
              <h3 class="text-lg font-bold text-gray-800 mb-4 flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-pink-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
                </svg>
                Contacto
              </h3>
              <ul class="space-y-4">
                <li class="bg-white rounded-lg shadow-sm p-3 border border-pink-100 hover:shadow-md transition-shadow flex items-center">
                  <div class="w-10 h-10 rounded-full bg-green-100 flex items-center justify-center mr-3">
                    <MessageCircle class="w-5 h-5 text-green-600" />
                  </div>
                  <div>
                    <p class="text-xs text-gray-500 mb-1">Escríbenos por WhatsApp</p>
                    <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
                       class="text-sm font-medium text-green-600 hover:text-green-700 transition-colors">+503 7201-1707</a>
                  </div>
                </li>
                <li class="bg-white rounded-lg shadow-sm p-3 border border-pink-100 hover:shadow-md transition-shadow flex items-center">
                  <div class="w-10 h-10 rounded-full bg-pink-100 flex items-center justify-center mr-3">
                    <Instagram class="w-5 h-5 text-pink-600" />
                  </div>
                  <div>
                    <p class="text-xs text-gray-500 mb-1">Síguenos en Instagram</p>
                    <a :href="instagramProfileUrl" target="_blank" rel="noopener noreferrer"
                       class="text-sm font-medium text-pink-600 hover:text-pink-700 transition-colors">@joyeria_tu_estilo_</a>
                  </div>
                </li>
              </ul>
            </div>
          </div>
          
          <!-- Copyright y derechos reservados -->
          <div class="mt-12 pt-8 border-t border-pink-100 flex flex-col md:flex-row justify-between items-center">
            <p class="text-gray-500 text-sm mb-4 md:mb-0 flex items-center">
              <span class="mr-2">© {{ new Date().getFullYear() }}</span>
              <span class="text-pink-500 font-dancing text-lg">Joyería Tu Estilo</span>
              <span class="ml-2">• Todos los derechos reservados.</span>
            </p>
            <div class="flex items-center">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-pink-400 mr-2" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M3.172 5.172a4 4 0 015.656 0L10 6.343l1.172-1.171a4 4 0 115.656 5.656L10 17.657l-6.828-6.829a4 4 0 010-5.656z" clip-rule="evenodd" />
              </svg>
              <span class="text-sm text-gray-500">Hecho con amor en El Salvador</span>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, computed, watch, nextTick } from 'vue'
import { Home, ShoppingBag, MapPin, Phone, Instagram, MessageCircle, Menu, X, RefreshCw, ChevronLeft, ChevronRight } from 'lucide-vue-next'

// Variables reactivas básicas
const showMobileMenu = ref(false)
const isScrolled = ref(false)
const activeSection = ref('inicio')
const loadingProducts = ref(false)
const errorProducts = ref(null)
const productPosts = ref([
  {
    id: '1',
    media_url: 'https://images.unsplash.com/photo-1515562141207-7a88fb7ce338',
    caption: 'Anillos de plata con piedras naturales',
    permalink: 'https://instagram.com/'
  },
  {
    id: '2',
    media_url: 'https://images.unsplash.com/photo-1588444837495-c6cfeb53f32d',
    caption: 'Aretes artesanales',
    permalink: 'https://instagram.com/'
  },
  {
    id: '3',
    media_url: 'https://images.unsplash.com/photo-1601821765780-754fa98637c1',
    caption: 'Collares premium',
    permalink: 'https://instagram.com/'
  }
])

// URLs y constantes
const instagramUrl = 'https://www.instagram.com/joyeria_tu_estilo_?utm_source=ig_web_button_share_sheet&igsh=ZDNlZDc0MzIxNw=='
const whatsappUrl = 'https://wa.me/+50372011707'
const instagramProfileUrl = 'https://www.instagram.com/joyeria_tu_estilo_'
const whatsAppNumber = '+50372011707'

// URLs para Google Maps - Ubicaciones reales
const ubicacionPrincipalMapUrl = 'https://maps.app.goo.gl/LMYa7VkPFrB7qrB6A' // URL de "Frente a Piscinas El Capulín"
const parqueCentralMoncaguaMapUrl = 'https://maps.app.goo.gl/m4XvPTPcTNF76f5s7' // URL del Parque Central de Moncagua
const parqueCentralSanMiguelMapUrl = 'https://maps.app.goo.gl/oS6yQumXy4rGkUp8A' // URL del Parque Central de San Miguel
const plazaElEncuentroMapUrl = 'https://maps.app.goo.gl/B4SvTx8KFFfv77ST6' // URL de Plaza El Encuentro, San Miguel
const metrocentroSanMiguelMapUrl = 'https://maps.app.goo.gl/ZES3cGc9rSDj4wH28' // URL de Metrocentro San Miguel
const ubicacionIframeUrl = 'https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d7720.126698999227!2d-88.28469367959394!3d13.4786719!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x8f7b48351bd0314b%3A0x54eb4d1462ca5a33!2sMoncagua%2C%20El%20Salvador!5e0!3m2!1ses!2ses!4v1718376881776!5m2!1ses!2ses' // URL del iframe de Google Maps

// Emojis de corazones y elementos románticos
const heartEmojis = ['💖', '💕', '💞', '💕', '💟', '✨', '💌', '🌟', '♥️', '💝']

// Items del menú
const menuItems = [
  { id: 'inicio', text: 'Inicio', href: '#inicio', icon: Home },
  { id: 'productos', text: 'Productos', href: '#productos', icon: ShoppingBag },
  { id: 'ubicacion', text: 'Ubicación', href: '#ubicacion', icon: MapPin },
  { id: 'contacto', text: 'Contacto', href: '#contacto', icon: Phone }
]

// Variables y funciones del slider
const slidesData = [
  {
    image: 'https://images.unsplash.com/photo-1515562141207-7a88fb7ce338?q=80&w=1920',
    title: '✨ Colección Primavera 2025',
    description: 'Descubre nuestras piezas más exclusivas en oro rosa y piedras naturales'
  },
  {
    image: 'https://images.unsplash.com/photo-1588444837495-c6cfeb53f32d?q=80&w=1920',
    title: '💎 Aretes Artesanales',
    description: 'Cada pieza cuenta una historia única, diseñada para brillar'
  },
  {
    image: 'https://images.unsplash.com/photo-1601821765780-754fa98637c1?q=80&w=1920',
    title: '💫 Collares Premium',
    description: 'Elegancia atemporal en cada detalle'
  },
  {
    image: 'https://images.unsplash.com/photo-1603561596112-0a132b757442?q=80&w=1920',
    title: '💝 Regalos Especiales',
    description: 'El detalle perfecto para esa persona única'
  },
  {
    image: 'https://images.unsplash.com/photo-1611955167811-4711904bb9f8?q=80&w=1920',
    title: '👑 Nueva Colección',
    description: 'Diseños exclusivos que definen tu estilo'
  }
]

const slides = ref(slidesData)
const currentSlide = ref(0)
const autoplayInterval = ref(null)
const autoplayDelay = 5000 // 5 segundos por slide

// Funciones del slider
const nextSlide = () => {
  currentSlide.value = (currentSlide.value + 1) % slides.value.length
}

const prevSlide = () => {
  currentSlide.value = (currentSlide.value - 1 + slides.value.length) % slides.value.length
}

const setSlide = (index) => {
  currentSlide.value = index
  restartAutoplay()
}

const startAutoplay = () => {
  stopAutoplay()
  autoplayInterval.value = setInterval(() => {
    nextSlide()
  }, autoplayDelay)
}

const stopAutoplay = () => {
  if (autoplayInterval.value) {
    clearInterval(autoplayInterval.value)
    autoplayInterval.value = null
  }
}

const restartAutoplay = () => {
  stopAutoplay()
  startAutoplay()
}

// Funciones de navegación y utilidades
const toggleMobileMenu = () => {
  showMobileMenu.value = !showMobileMenu.value
  
  // Controla el scroll del body cuando el menú está abierto
  if (showMobileMenu.value) {
    document.body.classList.add('overflow-hidden')
    // Añadir un evento de escape para cerrar el menú con la tecla ESC
    document.addEventListener('keydown', handleEscKeypress)
  } else {
    document.body.classList.remove('overflow-hidden')
    // Remover el evento cuando se cierra el menú
    document.removeEventListener('keydown', handleEscKeypress)
  }
}

// Función para cerrar el menú con la tecla ESC
const handleEscKeypress = (event) => {
  if (event.key === 'Escape' && showMobileMenu.value) {
    toggleMobileMenu()
  }
}

const scrollToSection = (sectionId) => {
  const element = document.getElementById(sectionId)
  if (element) {
    const headerOffset = 80
    const elementPosition = element.getBoundingClientRect().top
    const offsetPosition = elementPosition + window.pageYOffset - headerOffset
    
    window.scrollTo({
      top: offsetPosition,
      behavior: 'smooth'
    })
    
    activeSection.value = sectionId
    return true
  }
  return false
}

const handleMobileNavClick = (event, sectionId) => {
  event.preventDefault()
  const scrolled = scrollToSection(sectionId)
  
  if (scrolled) {
    // Solo cerramos el menú si pudimos desplazarnos a la sección
    toggleMobileMenu()
  } else {
    console.error(`Sección no encontrada: ${sectionId}`)
  }
}

// Funciones de productos
const getProductCategory = (product) => {
  const caption = product.caption?.toLowerCase() || ''
  if (caption.includes('anillo')) return 'Anillos'
  if (caption.includes('arete')) return 'Aretes'
  if (caption.includes('collar')) return 'Collares'
  if (caption.includes('pulsera')) return 'Pulseras'
  return 'Joyería'
}

const getProductTitle = (product) => {
  const caption = product.caption || ''
  const firstLine = caption.split('\n')[0]
  return firstLine.length > 50 ? firstLine.substring(0, 50) + '...' : firstLine
}

const getWhatsAppLinkForProduct = (product) => {
  const message = `Hola, me interesa este producto: ${product.permalink}`
  return `https://wa.me/${whatsAppNumber}?text=${encodeURIComponent(message)}`
}

// Handler para el evento de scroll
const handleScrollEvent = () => {
  isScrolled.value = window.scrollY > 10
  handleScroll()
  
  // Actualizar sección activa en el scroll
  updateActiveSection()
}

// Función para actualizar qué sección está activa basada en el scroll
const updateActiveSection = () => {
  const sections = menuItems.map(item => item.id);
  
  // Añadir punto y coma después de cada declaración
  const mouseX = event.clientX / window.innerWidth;
  const mouseY = event.clientY / window.innerHeight;
  const factor = 1.0;
  
  document.querySelectorAll('.heart').forEach(heart => {
    // Calcular offset limitado para mantener corazones en los bordes
    const offsetX = (mouseX - 0.5) * 20 * factor;
    const offsetY = (mouseY - 0.5) * 20 * factor;
    
    // Si el mouse está sobre contenido, disminuir la opacidad
    if (isMouseOverContent()) {
      heart.style.opacity = '0.15';
    } else {
      // Aplicar un efecto de brillo a los emojis cercanos al cursor
      const heartRect = heart.getBoundingClientRect();
      const heartCenterX = heartRect.left + heartRect.width / 2;
      const heartCenterY = heartRect.top + heartRect.height / 2;
      const distance = Math.sqrt(
        Math.pow(event.clientX - heartCenterX, 2) + 
        Math.pow(event.clientY - heartCenterY, 2)
      );
      
      // Si el cursor está cerca, aumentar ligeramente la opacidad y el brillo
      if (distance < 150) {
        heart.style.opacity = '0.4';
        heart.style.filter = 'drop-shadow(0 0 8px rgba(255, 105, 180, 0.4))';
      } else {
        heart.style.opacity = '0.3';
        heart.style.filter = 'drop-shadow(0 0 5px rgba(255, 20, 147, 0.2))';
      }
    }
    
    // Movimiento más suave para corazones
    heart.style.transition = 'transform 1s ease-out, opacity 0.5s ease-out';
    heart.style.transform = `translate(${offsetX}px, ${offsetY}px) scale(${1.1 + (mouseX * 0.2)}) rotate(${window.scrollY * 0.1}deg)`;
  });
};


// Nueva sección para los posts de Instagram
const instagramPosts = [
  {
    url: 'https://www.instagram.com/p/C3SvzQerlh_/'
  },
  {
    url: 'https://www.instagram.com/p/CxL56E8v-Zt/'
  },
  {
    url: 'https://www.instagram.com/p/Cmo_djMp4NV/'
  },
  {
    url: 'https://www.instagram.com/p/Ckja9m1PpMu/'
  },
  {
    url: 'https://www.instagram.com/reel/DExR3BgpaOm/'
  }
]

// Aplicar estilos a los posts de Instagram
const instagramPostStyle = computed(() => ({
  background: '#FFF',
  border: '0',
  borderRadius: '15px',
  boxShadow: '0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15)',
  margin: '1px',
  maxWidth: '540px',
  minWidth: '326px',
  padding: '0',
  width: '99.375%'
}))

onMounted(() => {
  // Cargar los posts de Instagram
  loadInstagramEmbed()
  
  // Recargar cuando cambien los posts
  watch(instagramPosts, () => {
    nextTick(() => {
      loadInstagramEmbed()
    })
  })
})

const loadInstagramEmbed = () => {
  if (window.instgrm) {
    window.instgrm.Embeds.process()
  } else {
    const script = document.createElement('script')
    script.async = true
    script.src = '//www.instagram.com/embed.js'
    document.body.appendChild(script)
    script.onload = () => {
      window.instgrm.Embeds.process()
    }
  }
}
</script>

<style>
/* Estilos para el título en el header */
.text-title {
  font-size: 1.8rem;
  font-weight: 600;
  background: linear-gradient(to right, #ff69b4, #ff1493);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
}

.animate-title {
  animation: titleFloat 3s ease-in-out infinite;
}

@keyframes titleFloat {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-5px);
  }
  100% {
    transform: translateY(0);
  }
}

/* Aseguramos que el texto sea responsivo en móviles */
@media (max-width: 640px) {
  .text-title {
    font-size: 1.4rem;
  }
}

/* Estilos para el slider adaptativo */
.slider-container {
  position: relative;
  width:  100%;
  overflow: hidden;
  border-radius: 1rem;
}

/* Animación de joyas flotantes */
.jewels-animation {
  position: fixed;
  width: 100%;
  height: 100vh;
  pointer-events: none;
}

.jewel {
  width: 40px;
  height: 40px;
  animation: float 25s infinite ease-in-out;
  background-size: cover;
  background-position: center;
  box-shadow: 0 8px 25px rgba(255, 105, 180, 0.2);
  opacity: 0.25;
  transform-origin: center;
  z-index: 100;
  filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.3));
  pointer-events: none; /* Asegura que no interfiera con clics */
}

.jewel::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, rgba(255, 255, 255, 0.6) 10%, transparent 70%);
  mix-blend-mode: overlay;
}

/* Formas específicas de joyas - posicionadas principalmente en los bordes */
.jewel-1 { 
  left: 2%; top: 5%; animation-delay: 0s; width: 40px; height: 40px; 
  background-image: url('https://images.unsplash.com/photo-1515562141207-7a88fb7ce338?w=100');
  border-radius: 50%; /* Forma circular para anillos */
}
.jewel-2 { 
  left: 3%; top: 25%; animation-delay: 1s; width: 35px; height: 50px; 
  background-image: url('https://images.unsplash.com/photo-1588444837495-c6cfeb53f32d?w=100');
  border-radius: 35% 35% 5% 5%; /* Forma para aretes colgantes */
  transform: rotate(0deg);
}
.jewel-3 { 
  left: 5%; top: 45%; animation-delay: 2s; width: 45px; height: 20px; 
  background-image: url('https://images.unsplash.com/photo-1601821765780-754fa98637c1?w=100');
  border-radius: 30px; /* Forma alargada para pulseras */
}
.jewel-4 { 
  left: 2%; top: 65%; animation-delay: 3s; width: 45px; height: 45px; 
  background-image: url('https://images.unsplash.com/photo-1603561596112-0a132b757442?w=100');
  border-radius: 8px; /* Forma cuadrada para piedras */
}
.jewel-5 { 
  left: 4%; top: 85%; animation-delay: 4s; width: 30px; height: 60px; 
  background-image: url('https://images.unsplash.com/photo-1611955167811-4711904bb9f8?w=100');
  border-radius: 50% 50% 5% 5%; /* Forma para pendientes largos */
}
.jewel-6 { 
  right: 2%; top: 5%; animation-delay: 5s; width: 35px; height: 35px; 
  background-image: url('https://images.unsplash.com/photo-1515562141207-7a88fb7ce338?w=100');
  border-radius: 50%; /* Forma circular para anillos */
}
.jewel-7 { 
  right: 4%; top: 25%; animation-delay: 6s; width: 45px; height: 25px; 
  background-image: url('https://images.unsplash.com/photo-1588444837495-c6cfeb53f32d?w=100');
  border-radius: 0% 0% 50% 50%; /* Forma de media luna para diademas */
}
.jewel-8 { 
  right: 3%; top: 45%; animation-delay: 7s; width: 25px; height: 60px; 
  background-image: url('https://images.unsplash.com/photo-1601821765780-754fa98637c1?w=100');
  border-radius: 0 0 50% 50%; /* Forma para collares colgantes */
}
.jewel-9 { 
  right: 5%; top: 65%; animation-delay: 8s; width: 50px; height: 20px; 
  background-image: url('https://images.unsplash.com/photo-1603561596112-0a132b757442?w=100');
  border-radius: 25px; /* Forma para brazaletes */
}
.jewel-10 { 
  right: 2%; top: 85%; animation-delay: 9s; width: 40px; height: 40px; 
  background-image: url('https://images.unsplash.com/photo-1611955167811-4711904bb9f8?w=100');
  border-radius: 5px; /* Forma cuadrada para dijes */
  transform: rotate(45deg);
}
.jewel-11 { 
  left: 15%; bottom: 2%; animation-delay: 10s; width: 30px; height: 45px; 
  background-image: url('https://images.unsplash.com/photo-1515562141207-7a88fb7ce338?w=100');
  border-radius: 50% 50% 0 0; /* Forma para aretes de botón */
}
.jewel-12 { 
  right: 15%; bottom: 2%; animation-delay: 11s; width: 55px; height: 20px; 
  background-image: url('https://images.unsplash.com/photo-1588444837495-c6cfeb53f32d?w=100');
  border-radius: 25px; /* Forma para pulseras */
}
.jewel-13 { 
  left: 35%; bottom: 5%; animation-delay: 12s; width: 30px; height: 30px; 
  background-image: url('https://images.unsplash.com/photo-1601821765780-754fa98637c1?w=100');
  border-radius: 50%; /* Forma circular para anillos */
  transform: rotate(30deg);
}
.jewel-14 { 
  right: 35%; bottom: 5%; animation-delay: 13s; width: 40px; height: 50px; 
  background-image: url('https://images.unsplash.com/photo-1603561596112-0a132b757442?w=100');
  border-radius: 50% 50% 10% 10%; /* Forma para colgantes */
}
.jewel-15 { 
  left: 50%; bottom: 3%; animation-delay: 14s; width: 45px; height: 25px; 
  background-image: url('https://images.unsplash.com/photo-1611955167811-4711904bb9f8?w=100');
  border-radius: 25px; /* Forma para broches */
}

/* Animación para los emojis de corazones */
.heart {
  animation: floatHeart 18s infinite ease-in-out;
  font-size: 30px;
  filter: drop-shadow(0 0 5px rgba(255, 20, 147, 0.2));
  z-index: 100;
  opacity: 0.3;
  pointer-events: none;
  background: linear-gradient(45deg, rgba(255, 255, 255, 0), rgba(255, 255, 255, 0.1));
  border-radius: 50%;
  padding: 5px;
}

.heart-1 { left: 6%; top: 8%; animation-delay: 0.5s; }
.heart-2 { right: 6%; top: 8%; animation-delay: 2s; }
.heart-3 { left: 5%; top: 35%; animation-delay: 3.5s; }
.heart-4 { right: 5%; top: 35%; animation-delay: 5s; }
.heart-5 { left: 4%; top: 65%; animation-delay: 6.5s; }
.heart-6 { right: 4%; top: 65%; animation-delay: 8s; }
.heart-7 { left: 10%; bottom: 5%; animation-delay: 9.5s; }
.heart-8 { right: 10%; bottom: 5%; animation-delay: 11s; }
.heart-9 { left: 30%; bottom: 8%; animation-delay: 12.5s; }
.heart-10 { right: 30%; bottom: 8%; animation-delay: 14s; }

@keyframes float {
  0% {
    transform: translateX(0) translateY(100vh) rotate(0deg) scale(0.8);
    opacity: 0;
  }
  10% {
    opacity: 0.2;
  }
  20% {
    transform: translateX(20px) translateY(80vh) rotate(90deg) scale(1);
  }
  40% {
    transform: translateX(-20px) translateY(60vh) rotate(180deg) scale(1.1);
    opacity: 0.25;
  }
  60% {
    transform: translateX(15px) translateY(40vh) rotate(270deg) scale(1);
  }
  80% {
    transform: translateX(-15px) translateY(20vh) rotate(320deg) scale(0.9);
    opacity: 0.2;
  }
  100% {
    transform: translateX(0) translateY(-100px) rotate(360deg) scale(0.7);
    opacity: 0;
  }
}

@keyframes floatHeart {
  0% {
    transform: translateX(0) translateY(100vh) scale(0.8) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 0.2;
  }
  30% {
    transform: translateX(15px) translateY(70vh) scale(1.2) rotate(10deg);
  }
  50% {
    transform: translateX(-15px) translateY(40vh) scale(1.3) rotate(-10deg);
    opacity: 0.3;
  }
  70% {
    transform: translateX(10px) translateY(20vh) scale(1.1) rotate(5deg);
  }
  90% {
    opacity: 0.15;
  }
  100% {
    transform: translateX(0) translateY(-50px) scale(0.7) rotate(0deg);
    opacity: 0;
  }
}

.slider-wrapper {
  display: flex;
  transition: transform 0.5s ease-in-out;
}

/* Animación para cambio de color del texto */
.changing-text {
  animation: colorChange 8s infinite;
  font-weight: 500;
}

@keyframes colorChange {
  0% { color: #f472b6; } /* pink-400 */
  25% { color: #db2777; } /* pink-600 */
  50% { color: #be185d; } /* pink-700 */
  75% { color: #9d174d; } /* pink-800 */
  100% { color: #f472b6; } /* pink-400 */
}

/* Animación para el texto con movimiento suave */
.animated-text {
  animation: floatText 3s ease-in-out infinite;
  background: linear-gradient(to right, #f472b6, #db2777);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  font-weight: 500;
}

@keyframes floatText {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

/* Animación para palabras rotativas */
.rotating-words {
  height: 1.5em;
  overflow: hidden;
  margin-top: 1rem;
}

.word-rotation {
  display: inline-flex;
  flex-direction: column;
  animation: rotate 12s cubic-bezier(0.68, -0.55, 0.27, 1.55) infinite;
}

.word-rotation span {
  display: block;
  height: 1.5em;
  font-weight: 600;
  background: linear-gradient(to right, #ec4899, #db2777);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  padding: 0 0.5rem;
}

@keyframes rotate {
  0%, 20% { transform: translateY(0); }
  25%, 45% { transform: translateY(-1.5em); }
  50%, 70% { transform: translateY(-3em); }
  75%, 95% { transform: translateY(-4.5em); }
  100% { transform: translateY(-6em); }
}
</style>
