<template>
  <div class="min-h-screen relative">
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
            </div>

            <!-- Botón menú móvil -->
            <button 
              @click="toggleMobileMenu" 
              class="md:hidden relative flex items-center space-x-1 px-3 py-2 rounded-full bg-gradient-to-r from-pink-500 to-purple-500 text-white hover:shadow-md transition-all duration-300"
            >
              <Menu v-if="!showMobileMenu" class="w-5 h-5" />
              <X v-else class="w-5 h-5" />
              <span class="text-sm font-medium">Menú</span>
            </button>
          </div>
        </nav>
      </header>

      <!-- Hero Section con Slider -->
      <section id="inicio" class="pt-24 pb-16 bg-gradient-to-br from-pink-50 via-pink-100/30 to-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <!-- Textos del hero -->
          <div class="text-center mb-12 md:mb-16 relative">
            <h2 class="animate-text text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-pink-500 to-pink-400 mb-4 transform hover:scale-105 transition-transform duration-300">
              Encuentra La Joya Perfecta
            </h2>
            <p class="animate-text text-lg sm:text-xl md:text-2xl mt-6 text-gray-600 max-w-3xl mx-auto leading-relaxed">
              Accesorios exclusivos para complementar tu estilo único
            </p>
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
      
      <!-- Sección de Productos con Posts de Instagram -->
      <section id="productos" class="py-16 bg-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="text-center mb-12">
            <h2 class="text-3xl md:text-4xl font-bold mb-4">Nuestros Productos</h2>
            <p class="text-gray-600 max-w-3xl mx-auto">
              Descubre nuestra selección de joyería exclusiva, diseñada para destacar tu belleza y personalidad
            </p>
          </div>
          
          <!-- Loading State para Productos -->
          <div v-if="loadingProducts" class="flex justify-center items-center py-12">
            <RefreshCw class="w-8 h-8 text-pink-500 animate-spin" />
          </div>
          
          <!-- Error State para Productos -->
          <div v-else-if="errorProducts" class="bg-red-50 text-red-500 p-4 rounded-lg text-center">
            {{ errorProducts }}
          </div>
          
          <!-- Grid de Productos -->
          <div v-else class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
            <div 
              v-for="product in productPosts" 
              :key="product.id"
              class="group bg-white rounded-xl overflow-hidden shadow-md hover:shadow-xl transition-all duration-300 transform hover:-translate-y-2"
            >
              <!-- Imagen del Producto -->
              <div class="aspect-square overflow-hidden relative">
                <img 
                  :src="product.media_url" 
                  :alt="product.caption || 'Producto de joyería'"
                  class="w-full h-full object-cover transition-transform duration-700 group-hover:scale-110"
                />
                <div class="absolute inset-0 bg-gradient-to-t from-black/50 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
                
                <!-- Etiqueta de categoría -->
                <div class="absolute top-4 left-4">
                  <span class="px-3 py-1 bg-white/90 backdrop-blur-sm text-pink-500 text-sm font-medium rounded-full shadow-sm">
                    {{ getProductCategory(product) }}
                  </span>
                </div>
              </div>
              
              <!-- Información del Producto -->
              <div class="p-5">
                <h3 class="text-lg font-bold text-gray-900 group-hover:text-pink-500 transition-colors mb-2">
                  {{ getProductTitle(product) }}
                </h3>
                <p class="text-sm text-gray-600 mb-4 line-clamp-3">
                  {{ product.caption }}
                </p>
                
                <!-- Botones de Acción -->
                <div class="flex space-x-2">
                  <a 
                    :href="product.permalink" 
                    target="_blank" 
                    rel="noopener noreferrer"
                    class="flex-1 inline-flex items-center justify-center px-4 py-2 bg-pink-50 hover:bg-pink-100 text-pink-500 font-medium rounded-lg transition-colors text-sm"
                  >
                    <Instagram class="w-4 h-4 mr-2" />
                    Ver en Instagram
                  </a>
                  <a 
                    :href="getWhatsAppLinkForProduct(product)" 
                    target="_blank" 
                    rel="noopener noreferrer"
                    class="flex-1 inline-flex items-center justify-center px-4 py-2 bg-green-50 hover:bg-green-100 text-green-500 font-medium rounded-lg transition-colors text-sm"
                  >
                    <MessageCircle class="w-4 h-4 mr-2" />
                    Consultar
                  </a>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Ver más productos -->
          <div class="text-center mt-12">
            <a 
              :href="instagramProfileUrl" 
              target="_blank" 
              rel="noopener noreferrer"
              class="inline-flex items-center px-6 py-3 bg-white border-2 border-pink-500 text-pink-500 hover:bg-pink-50 font-medium rounded-lg shadow-sm hover:shadow-md transition-all duration-300"
            >
              <ShoppingBag class="w-5 h-5 mr-2" />
              Ver más productos
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
          </div>
          
          <!-- Mapa principal de ubicación -->
          <div class="mb-10 bg-white rounded-xl shadow-lg overflow-hidden">            <h3 class="text-xl font-bold p-4 bg-gradient-to-r from-pink-500 to-purple-500 text-white flex items-center">
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
                </p>
                <div class="flex flex-wrap gap-2 mt-3">                  <a 
                    :href="whatsappUrl"
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
            </div>            <div class="aspect-[16/9] w-full bg-gray-100 rounded-lg relative overflow-hidden">
              <div class="absolute inset-0 flex flex-col items-center justify-center p-4 text-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-pink-500 mb-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                </svg>
                <h3 class="text-xl font-medium mb-2">Moncagua, San Miguel</h3>
                <p class="text-gray-600 mb-4">Frente a las piscinas El Capulín</p>
                <a 
                  href="https://maps.app.goo.gl/hytGZs9mdFftJ4xm8" 
                  target="_blank" 
                  class="px-4 py-2 bg-pink-500 text-white rounded-lg hover:bg-pink-600 transition-all"
                >
                  Ver en Google Maps
                </a>
              </div>
            </div>
          </div>
          
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 gap-8 max-w-4xl mx-auto">
            <!-- Punto de entrega 1 -->
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100">
              <div class="flex items-start">
                <div class="bg-pink-100 rounded-full p-3 mr-4">
                  <MapPin class="w-6 h-6 text-pink-500" />
                </div>
                <div>
                  <h3 class="text-xl font-bold mb-2">1. Parque Central de Moncagua</h3>
                  <p class="text-gray-600 mb-4">Punto de encuentro principal en el centro de la ciudad</p>
                  <a 
                    href="https://maps.app.goo.gl/hytGZs9mdFftJ4xm8" 
                    target="_blank" 
                    class="text-pink-500 hover:text-pink-700 inline-flex items-center"
                  >
                    <span>Ver en mapa</span>
                    <svg class="w-4 h-4 ml-1" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                  </a>
                </div>
              </div>
            </div>
            
            <!-- Punto de entrega 2 -->
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100">
              <div class="flex items-start">
                <div class="bg-pink-100 rounded-full p-3 mr-4">
                  <MapPin class="w-6 h-6 text-pink-500" />
                </div>
                <div>
                  <h3 class="text-xl font-bold mb-2">2. Parque Central de San Miguel</h3>
                  <p class="text-gray-600 mb-4">Ubicado en el corazón de San Miguel, fácil acceso</p>
                  <a 
                    href="https://maps.app.goo.gl/zBq83T7JUmQJPMQS9" 
                    target="_blank" 
                    class="text-pink-500 hover:text-pink-700 inline-flex items-center"
                  >
                    <span>Ver en mapa</span>
                    <svg class="w-4 h-4 ml-1" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                  </a>
                </div>
              </div>
            </div>
            
            <!-- Punto de entrega 3 -->
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100">
              <div class="flex items-start">
                <div class="bg-pink-100 rounded-full p-3 mr-4">
                  <MapPin class="w-6 h-6 text-pink-500" />
                </div>
                <div>
                  <h3 class="text-xl font-bold mb-2">3. Plaza El Encuentro, San Miguel</h3>
                  <p class="text-gray-600 mb-4">Plaza comercial with amplio estacionamiento</p>
                  <a 
                    href="https://maps.app.goo.gl/UgxLzehjZ7UbT4LeA" 
                    target="_blank" 
                    class="text-pink-500 hover:text-pink-700 inline-flex items-center"
                  >
                    <span>Ver en mapa</span>
                    <svg class="w-4 h-4 ml-1" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                  </a>
                </div>
              </div>
            </div>
            
            <!-- Punto de entrega 4 -->
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1 border border-pink-100">
              <div class="flex items-start">
                <div class="bg-pink-100 rounded-full p-3 mr-4">
                  <MapPin class="w-6 h-6 text-pink-500" />
                </div>
                <div>
                  <h3 class="text-xl font-bold mb-2">4. Metrocentro San Miguel</h3>
                  <p class="text-gray-600 mb-4">Centro comercial con facilidad de acceso y seguridad</p>
                  <a 
                    href="https://maps.app.goo.gl/GVxY6QzcPZFJZqa46" 
                    target="_blank" 
                    class="text-pink-500 hover:text-pink-700 inline-flex items-center"
                  >
                    <span>Ver en mapa</span>
                    <svg class="w-4 h-4 ml-1" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                  </a>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Información adicional sobre entregas -->
          <div class="mt-12 text-center bg-pink-50 p-6 rounded-xl max-w-2xl mx-auto">
            <h3 class="text-xl font-bold mb-2">¿Cómo funcionan las entregas?</h3>
            <ol class="text-left space-y-3 mt-4">
              <li class="flex items-start">
                <span class="flex-shrink-0 bg-pink-200 text-pink-700 rounded-full w-6 h-6 flex items-center justify-center mr-2">1</span>
                <span>Selecciona tus productos y contáctanos por WhatsApp</span>
              </li>
              <li class="flex items-start">
                <span class="flex-shrink-0 bg-pink-200 text-pink-700 rounded-full w-6 h-6 flex items-center justify-center mr-2">2</span>
                <span>Coordina día, hora y punto de entrega que prefieras</span>
              </li>
              <li class="flex items-start">
                <span class="flex-shrink-0 bg-pink-200 text-pink-700 rounded-full w-6 h-6 flex items-center justify-center mr-2">3</span>
                <span>Realiza tu pago y recibe tu producto</span>
              </li>
            </ol>
            <div class="mt-6">
              <a 
                :href="whatsappUrl" 
                target="_blank" 
                rel="noopener noreferrer"
                class="inline-flex items-center px-5 py-3 bg-green-500 hover:bg-green-600 text-white font-medium rounded-full shadow-md transition-colors"
              >
                <MessageCircle class="w-5 h-5 mr-2" />
                Coordinar entrega
              </a>
            </div>
          </div>
        </div>
      </section>

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
                  <span>Horario de entregas: Lunes a Sábado, 9:00 AM - 6:00 PM</span>
                </div>
              </div>
                <div class="rounded-xl overflow-hidden mb-6 border border-gray-200 bg-gray-100 h-[350px] relative">
                <div class="absolute inset-0 flex flex-col items-center justify-center p-4 text-center">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-pink-500 mb-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                  </svg>
                  <h3 class="text-xl font-medium mb-2">Parque Central de Moncagua</h3>
                  <p class="text-gray-600 mb-4">Punto de entrega principal</p>
                  <a 
                    href="https://maps.app.goo.gl/hytGZs9mdFftJ4xm8" 
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
                </a>
                <a 
                  href="https://maps.app.goo.gl/hytGZs9mdFftJ4xm8" 
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
          </div>
        </div>
      </section>

      <!-- Footer -->
      <footer class="bg-white border-t border-gray-100 py-12">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            <div>
              <div class="flex items-center mb-4">
                <div class="w-10 h-10 bg-gradient-to-r from-pink-500 to-purple-500 rounded-full flex items-center justify-center mr-3">
                  <span class="text-xl">💖</span>
                </div>
                <h2 class="text-xl font-dancing font-bold bg-gradient-to-r from-pink-500 to-purple-500 bg-clip-text text-transparent">
                  Joyería Tu Estilo
                </h2>
              </div>
              <p class="text-gray-600 mb-4">
                Joyas únicas para momentos especiales. Encuentra la pieza perfecta para ti o para regalar.
              </p>
              <div class="flex space-x-4">
                <a :href="instagramUrl" target="_blank" rel="noopener noreferrer"
                   class="text-gray-400 hover:text-pink-500 transition-colors">
                  <Instagram class="w-5 h-5" />
                </a>
                <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
                   class="text-gray-400 hover:text-green-500 transition-colors">
                  <MessageCircle class="w-5 h-5" />
                </a>
              </div>
            </div>
            
            <div>
              <h3 class="text-lg font-medium mb-4">Enlaces Rápidos</h3>
              <ul class="space-y-2">
                <li>
                  <a href="#inicio" @click.prevent="scrollToSection('inicio')" 
                     class="text-gray-500 hover:text-pink-500 transition-colors">Inicio</a>
                </li>
                <li>
                  <a href="#productos" @click.prevent="scrollToSection('productos')" 
                     class="text-gray-500 hover:text-pink-500 transition-colors">Productos</a>
                </li>
                <li>
                  <a href="#ubicacion" @click.prevent="scrollToSection('ubicacion')" 
                     class="text-gray-500 hover:text-pink-500 transition-colors">Ubicación</a>
                </li>
                <li>
                  <a href="#contacto" @click.prevent="scrollToSection('contacto')" 
                     class="text-gray-500 hover:text-pink-500 transition-colors">Contacto</a>
                </li>
              </ul>
            </div>
            
            <div>
              <h3 class="text-lg font-medium mb-4">Contacto</h3>
              <ul class="space-y-2">
                <li class="flex items-center space-x-2">
                  <MessageCircle class="w-5 h-5 text-green-500" />
                  <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
                     class="text-gray-500 hover:text-green-500 transition-colors">WhatsApp</a>
                </li>
                <li class="flex items-center space-x-2">
                  <Instagram class="w-5 h-5 text-pink-500" />
                  <a :href="instagramUrl" target="_blank" rel="noopener noreferrer"
                     class="text-gray-500 hover:text-pink-500 transition-colors">Instagram</a>
                </li>
              </ul>
            </div>
          </div>
          
          <div class="mt-12 pt-8 border-t border-gray-100 text-center">
            <p class="text-gray-500 text-sm">
              © {{ new Date().getFullYear() }} Joyería Tu Estilo. Todos los derechos reservados.
            </p>
          </div>
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, computed } from 'vue'
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
  if (showMobileMenu.value) {
    document.body.classList.add('overflow-hidden')
  } else {
    document.body.classList.remove('overflow-hidden')
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
  }
}

const handleMobileNavClick = (event, sectionId) => {
  event.preventDefault()
  scrollToSection(sectionId)
  toggleMobileMenu()
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

// Lifecycle hooks
onMounted(() => {
  startAutoplay()
  window.addEventListener('scroll', () => {
    isScrolled.value = window.scrollY > 10
  })
})

onBeforeUnmount(() => {
  stopAutoplay()
  window.removeEventListener('scroll', () => {
    isScrolled.value = window.scrollY > 10
  })
})
</script>

<style scoped>
/* Fuente personalizada - importar desde Google Fonts */
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&display=swap');

.font-dancing {
  font-family: 'Dancing Script', cursive;
}

/* Animaciones para menú móvil */
.mobile-nav-enter-active,
.mobile-nav-leave-active {
  transition: opacity 0.3s, transform 0.3s;
}

.mobile-nav-enter-from,
.mobile-nav-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

/* Transiciones específicas para el panel lateral */
.mobile-nav-enter-active .w-64,
.mobile-nav-leave-active .w-64 {
  transition: transform 0.3s ease-out;
}

.mobile-nav-enter-from .w-64,
.mobile-nav-leave-to .w-64 {
  transform: translateX(100%);
}

/* Media query para móviles */
@media (max-width: 768px) {
  .mobile-nav-link {
    width: 100%;
  }
}

/* Efecto de brillo */
.group:hover .shine {
  opacity: 1;
}

/* Animación de texto */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.animate-text {
  animation: fadeIn 0.8s ease-out forwards;
  opacity: 0;
}

.animate-text:nth-child(2) {
  animation-delay: 0.3s;
}

.animate-text:nth-child(3) {
  animation-delay: 0.6s;
}

/* Limitar líneas de texto */
.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* Animación para el texto del hero */
.animate-text {
  animation: fadeInUp 0.8s ease-out forwards;
  opacity: 0;
}

.animate-text:nth-child(2) {
  animation-delay: 0.2s;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Animaciones para el slider */
.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.5s ease;
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* Animaciones para los botones de navegación */
@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

.nav-button:hover {
  animation: pulse 1s infinite;
}

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

.slider-wrapper {
  display: flex;
  transition: transform 0.5s ease-in-out;
}

.slide {
  min-width: 100%;
  transition: opacity 0.5s;
}

.slide img {
  width: 100%;
  border-radius: 1rem;
}

.slide-content {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 1rem;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.7), transparent);
  color: white;
}

.slider-nav {
  position: absolute;
  top: 50%;
  width: 100%;
  display: flex;
  justify-content: space-between;
  transform: translateY(-50%);
}

.nav-button {
  background: rgba(255, 255, 255, 0.3);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background 0.3s;
}

.nav-button:hover {
  background: rgba(255, 255, 255, 0.5);
}

.nav-dot {
  height: 8px;
  width: 8px;
  margin: 0 4px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: transform 0.3s;
}

.nav-dot.active {
  transform: scale(1.2);
  background: white;
}
</style>
