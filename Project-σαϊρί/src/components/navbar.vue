<template>
  <div class="min-h-screen relative">
    <!-- Contenedor principal con z-index -->
    <div class="relative z-1">
      <!-- Header y contenido existente -->
      <header class="fixed top-0 left-0 right-0 z-50 bg-white shadow-md">
    <nav class="container mx-auto px-4 py-2">
      <div class="flex items-center justify-between">
        <div class="flex items-center">
          <router-link to="/" class="flex items-center">
            <img src="../assets/logo.png" alt="Logo Joyería Tu Estilo" class="w-12 h-12 mr-2">
            <span class="text-title font-dancing">Joyería Tu Estilo</span>
          </router-link>
        </div>
        
        <!-- Menú de escritorio mejorado -->
        <div class="hidden md:flex items-center space-x-8">
          <a 
            v-for="item in menuItems"
            :key="item.id"
            :href="item.href"
            @click.prevent="scrollToSection(item.id)"
            class="relative group flex items-center space-x-2 px-4 py-2 rounded-lg transition-all duration-300"
            :class="[
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
          <!-- Botones de redes sociales con animaciones y etiquetas -->
        <div class="hidden md:flex items-center space-x-4 ml-4 border-l pl-4 border-gray-200">
          <a :href="instagramUrl" target="_blank" rel="noopener noreferrer"
             class="flex items-center px-3 py-1.5 text-gray-600 hover:text-white hover:bg-gradient-to-r hover:from-pink-500 hover:to-purple-500 rounded-full transition-all duration-300 transform hover:scale-105 group">
            <Instagram class="w-5 h-5 mr-1.5 group-hover:text-white" />
            <span class="text-sm font-medium">Instagram</span>
          </a>
          <a :href="whatsappUrl" target="_blank" rel="noopener noreferrer"
             class="flex items-center px-3 py-1.5 text-gray-600 hover:text-white hover:bg-green-500 rounded-full transition-all duration-300 transform hover:scale-105 group">
            <MessageCircle class="w-5 h-5 mr-1.5 group-hover:text-white" />
            <span class="text-sm font-medium">WhatsApp</span>
          </a>
        </div>
          <!-- Botón menú móvil mejorado -->
        <button 
          @click="toggleMobileMenu" 
          class="md:hidden relative flex items-center space-x-1 px-3 py-2 rounded-full bg-gradient-to-r from-pink-500 to-purple-500 text-white hover:shadow-md transition-all duration-300"
          :aria-expanded="showMobileMenu"
          aria-label="Menú principal"
        >
          <div class="relative w-5 h-5">
            <transition name="menu-icon">
              <Menu v-if="!showMobileMenu" class="w-5 h-5" />
              <X v-else class="w-5 h-5" />
            </transition>
          </div>
          <span class="text-sm font-medium">Menú</span>
        </button>
      </div>
    </nav>
  </header>

      <!-- Hero Section con Instagram Posts -->
      <section id="inicio" class="pt-24 pb-16 bg-gradient-to-br from-pink-100 via-purple-50 to-white">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <!-- Textos del hero -->
          <div class="text-center mb-16">
            <h2 class="animate-text text-4xl md:text-6xl font-bold text-gray-800">
              Encuentra La Joya Perfecta
            </h2>
            <p class="animate-text text-xl md:text-2xl mt-6 text-gray-600 max-w-3xl mx-auto">
              Accesorios exclusivos para complementar tu estilo único
            </p>
          </div>
          
          <!-- Instagram Feed -->
          <div class="mb-8 text-center">
            <h3 class="text-2xl font-semibold mb-2">Nuestras Creaciones</h3>
            <p class="text-gray-600 mb-8">Síguenos en Instagram para ver nuestras últimas novedades</p>
          </div>
          
          <!-- Loading State -->
          <div v-if="loading" class="flex justify-center items-center py-12">
            <RefreshCw class="w-8 h-8 text-pink-500 animate-spin" />
          </div>
          
          <!-- Error State -->
          <div v-else-if="error" class="bg-red-50 text-red-500 p-4 rounded-lg text-center">
            {{ error }}
          </div>
          
          <!-- Instagram Grid -->
          <div v-else class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 mb-10">
            <div 
              v-for="post in instagramPosts" 
              :key="post.id"
              class="group bg-white rounded-xl overflow-hidden shadow-sm hover:shadow-lg transition-all duration-300 transform hover:-translate-y-1"
            >
              <!-- Imagen -->
              <div class="aspect-square relative overflow-hidden">
                <img 
                  :src="post.media_url" 
                  :alt="post.caption || 'Instagram post'"
                  class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-105"
                />
                <div class="absolute inset-0 bg-gradient-to-t from-black/60 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
                
                <!-- Overlay con caption -->
                <div class="absolute bottom-0 left-0 right-0 p-4 transform translate-y-full group-hover:translate-y-0 transition-transform duration-300">
                  <p class="text-white text-sm line-clamp-3">{{ post.caption }}</p>
                </div>
              </div>
              
              <!-- Acciones -->
              <div class="p-3 flex justify-between items-center">
                <a 
                  :href="post.permalink" 
                  target="_blank" 
                  rel="noopener noreferrer"
                  class="text-xs text-pink-500 hover:underline"
                >
                  Ver en Instagram
                </a>
                <a 
                  :href="getWhatsAppLink(post)" 
                  target="_blank" 
                  rel="noopener noreferrer"
                  class="flex items-center space-x-1 text-xs text-green-600 hover:underline"
                >
                  <MessageCircle class="w-3 h-3" />
                  <span>Consultar</span>
                </a>
              </div>
            </div>
          </div>
          
          <!-- Load More Button -->
          <div v-if="instagramPosts.length > 0 && hasMore" class="text-center mt-4 mb-8">
            <button 
              @click="loadMorePosts" 
              class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-pink-500 hover:bg-pink-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-pink-500 transition-colors"
              :disabled="loadingMore"
            >
              <RefreshCw v-if="loadingMore" class="w-4 h-4 mr-2 animate-spin" />
              <span>{{ loadingMore ? 'Cargando...' : 'Ver más' }}</span>
            </button>
          </div>
          
          <!-- Instagram Follow -->
          <div class="text-center mt-8">
            <a 
              :href="instagramProfileUrl" 
              target="_blank" 
              rel="noopener noreferrer"
              class="inline-flex items-center px-5 py-3 rounded-full bg-gradient-to-r from-pink-500 to-purple-500 text-white font-medium shadow-md hover:shadow-lg transform transition-all duration-300 hover:-translate-y-1"
            >
              <Instagram class="w-5 h-5 mr-2" />
              Síguenos en Instagram
            </a>
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
                    :href="getDirectionsWhatsAppLink()"
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
            </div>            <div class="aspect-[16/9] w-full">
              <iframe 
                src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3873.2669704581713!2d-88.25818468563658!3d13.53281860154693!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0x0!2zMTPCsDMyJzA0LjYiTiA4OMKwMTUnMjYuNSJX!5e0!3m2!1ses!2ssv!4v1654694325788!5m2!1ses!2ssv"
                width="100%" 
                height="450" 
                style="border:0;" 
                allowfullscreen="" 
                loading="lazy" 
                referrerpolicy="no-referrer-when-downgrade"
                class="w-full"
                title="Mapa de ubicación en Moncagua, frente a piscinas El Capulín"
              ></iframe>
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
              
              <div class="rounded-xl overflow-hidden mb-6 border border-gray-200">
                <iframe 
                  src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d969.3210042538222!2d-88.25632663032436!3d13.532683494407246!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x8f659c9a1e4a16bb%3A0xa4e16215ef01c63!2sParque%20Central%20de%20Moncagua!5e0!3m2!1ses!2ssv!4v1654694325788!5m2!1ses!2ssv"
                  width="100%" 
                  height="350" 
                  style="border:0;" 
                  allowfullscreen="" 
                  loading="lazy" 
                  referrerpolicy="no-referrer-when-downgrade"
                  class="w-full"
                  title="Mapa del Parque Central de Moncagua - Punto de entrega principal"
                ></iframe>
              </div>
              
              <div class="flex flex-wrap gap-3 justify-center">
                <a 
                  :href="getDirectionsWhatsAppLink()"
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
import { Home, ShoppingBag, MapPin, Phone, Instagram, MessageCircle, Menu, X, RefreshCw } from 'lucide-vue-next'

// Variables reactivas
const showMobileMenu = ref(false)
const isScrolled = ref(false)
const activeSection = ref('inicio')
const instagramPosts = ref([])
const productPosts = ref([])
const loading = ref(true)
const loadingProducts = ref(true)
const loadingMore = ref(false)
const error = ref(null)
const errorProducts = ref(null)
const hasMore = ref(true)
const nextPageToken = ref(null)

// Formulario de contacto
const contactForm = ref({
  name: '',
  email: '',
  phone: '',
  message: '',
  consent: false
})
const isSubmitting = ref(false)
const formSuccess = ref(false)
const formError = ref(null)

// Validación del formulario
const isFormValid = computed(() => {
  return (
    contactForm.value.name.trim() !== '' && 
    contactForm.value.email.trim() !== '' && 
    /^\S+@\S+\.\S+$/.test(contactForm.value.email) && 
    contactForm.value.message.trim() !== '' && 
    contactForm.value.consent
  )
})

// URLs de redes sociales
const instagramUrl = 'https://www.instagram.com/joyeria_tu_estilo_?utm_source=ig_web_button_share_sheet&igsh=ZDNlZDc0MzIxNw=='
const whatsappUrl = 'https://wa.me/+50372011707'
const instagramProfileUrl = 'https://www.instagram.com/joyeria_tu_estilo_/'
const whatsAppNumber = '+50372011707'

// Items del menú
const menuItems = [
  { id: 'inicio', text: 'Inicio', href: '#inicio', icon: Home },
  { id: 'productos', text: 'Productos', href: '#productos', icon: ShoppingBag },
  { id: 'ubicacion', text: 'Ubicación', href: '#ubicacion', icon: MapPin },
  { id: 'contacto', text: 'Contacto', href: '#contacto', icon: Phone }
]

// Métodos
const toggleMobileMenu = () => {
  showMobileMenu.value = !showMobileMenu.value
  if (showMobileMenu.value) {
    document.body.classList.add('overflow-hidden')
  } else {
    document.body.classList.remove('overflow-hidden')
  }
}

const handleMobileNavClick = (event, sectionId) => {
  event.preventDefault()
  scrollToSection(sectionId)
  toggleMobileMenu()
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

const handleScroll = () => {
  const scrollY = window.scrollY
  isScrolled.value = scrollY > 10
  
  // Detectar sección activa según el scroll
  const sections = menuItems.map(item => document.getElementById(item.id))
  const filteredSections = sections.filter(Boolean)
  
  if (filteredSections.length > 0) {
    for (let i = filteredSections.length - 1; i >= 0; i--) {
      const section = filteredSections[i]
      const rect = section.getBoundingClientRect()
      if (rect.top <= 150) {
        activeSection.value = section.id
        break
      }
    }
  }
}

// Función para obtener datos de demostración cuando no hay API disponible
const fetchInstagramFeedDemo = async () => {
  // Simulamos un retraso para que parezca una llamada a API real
  await new Promise(resolve => setTimeout(resolve, 800))
  
  // Posts de ejemplo para desarrollo
  const mockPosts = [
    {
      id: 'mock-1',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/416093578_925376515805519_6204587438407816069_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=108&_nc_ohc=l63U9V4DtbkAX-iZKMG&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfCgKC-nt33-gm9zB5DF1I1vwZ2RQYSv6YdUvw3a1Nx2Lg&oe=6599DFF9&_nc_sid=10d13b',
      caption: 'Anillos de plata con piedras naturales. Disponibles en todas las tallas. ✨💍 #Joyeria #Anillos',
      permalink: 'https://www.instagram.com/p/DExUD_hJQpn/',
      timestamp: new Date().toISOString()
    },
    {
      id: 'mock-2',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/418731064_934188625106273_8055244778637432844_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=101&_nc_ohc=LZYMFPWjn9MAX9s9ZRn&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfCsWpPfR-A1Hpqc-3TfZmH_SskrZcHIQS-PvRwHu0m-og&oe=659A9DEA&_nc_sid=10d13b',
      caption: 'Aretes artesanales con acabados únicos. El complemento perfecto para cualquier ocasión. 💎 #JoyeriaTuEstilo',
      permalink: 'https://www.instagram.com/p/DExUD_hJQpn/',
      timestamp: new Date().toISOString()
    },
    {
      id: 'mock-3',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/417961325_7074003449294461_1222855484328336521_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=105&_nc_ohc=FhgO2J5G6AIAX8loxcJ&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfCTxEF04tpDdN1IZNIe8vPjVGRYJ7_pAQVm-qxXbvgTEA&oe=659A61B6&_nc_sid=10d13b',
      caption: 'Collares de fantasía fina importados, acabados anti-alérgicos. Ideal para regalo. 🎁 #Collares #Moda',
      permalink: 'https://www.instagram.com/p/DExUD_hJQpn/',
      timestamp: new Date().toISOString()
    }
  ]

  return {
    data: mockPosts,
    paging: {
      cursors: {
        after: 'mock_token_1'
      },
      next: true
    }
  }
}

// Función para simular carga de más posts de demostración
const fetchMorePostsDemo = async () => {
  // Simulamos un retraso para que parezca una llamada a API real
  await new Promise(resolve => setTimeout(resolve, 1200))
  
  // Generamos más datos de ejemplo
  const mockPosts = [
    {
      id: 'mock-more-1',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/417870517_769218965282461_2280238888523666376_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=103&_nc_ohc=DqP20rF1MmwAX98zWHw&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfC4YVrFzIQNW4dLYrAYA4Ww93fkMlIqwTfh6MgGHhG2aA&oe=659A58D9&_nc_sid=10d13b',
      caption: 'Anillos de compromiso, el símbolo perfecto de tu amor. Consulta disponibilidad. 💍 #Compromiso',
      permalink: 'https://www.instagram.com/p/C9xCZ3dr4nS/',
      timestamp: new Date().toISOString()
    },
    {
      id: 'mock-more-2',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/418742654_826384052879566_6093242414607391628_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=109&_nc_ohc=fy27IXqyR0kAX92KwEp&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfA3wRNtCQlpYV5_99SrHQYcjNHx0JcyJrn8gS1zcwMTLg&oe=6599EDC5&_nc_sid=10d13b',
      caption: 'Aretes largos para eventos especiales. Elegancia y estilo garantizado. ✨ #EventosEspeciales',
      permalink: 'https://www.instagram.com/p/C9xCWxLLi52/',
      timestamp: new Date().toISOString()
    },
    {
      id: 'mock-more-3',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/417925913_1148328189728607_5631235117699863773_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=106&_nc_ohc=9uFfHX4o6UcAX9I0vIl&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfBP_D1-6GnxBu_oL3U_Oc1VjCSTUxgxc7P7YlvIbZUqHw&oe=659B10C6&_nc_sid=10d13b',
      caption: 'Collares minimalistas, perfectos para el día a día. Envíos a domicilio. 🚚 #EstiloMinimalista',
      permalink: 'https://www.instagram.com/p/C9xCSuJLX5O/',
      timestamp: new Date().toISOString()
    }
  ]

  return {
    data: mockPosts,
    paging: {
      cursors: {
        after: 'mock_token_2'
      },
      next: false
    }
  }
}

// Cargar posts de Instagram
const loadInstagramPosts = async () => {
  try {
    loading.value = true
    error.value = null
    
    // Obtener los posts de Instagram de nuestra función de demostración
    const instagramFeed = await fetchInstagramFeedDemo()
    instagramPosts.value = instagramFeed.data
    hasMore.value = !!instagramFeed.paging?.next
    nextPageToken.value = instagramFeed.paging?.cursors?.after || null
    
  } catch (e) {
    error.value = 'No se pudieron cargar las imágenes. Por favor, intenta de nuevo más tarde.'
    console.error('Error loading Instagram posts:', e)
  } finally {
    loading.value = false
  }
}

// Cargar más posts
const loadMorePosts = async () => {
  if (loadingMore.value || !hasMore.value) return
  
  try {
    loadingMore.value = true
    
    // Obtener más posts de demostración
    const morePosts = await fetchMorePostsDemo()
    if (morePosts.data.length > 0) {
      instagramPosts.value = [...instagramPosts.value, ...morePosts.data]
      hasMore.value = !!morePosts.paging?.next
      nextPageToken.value = morePosts.paging?.cursors?.after || null
    } else {
      hasMore.value = false
    }
    
  } catch (e) {
    error.value = 'Error al cargar más posts'
  } finally {
    loadingMore.value = false
  }
}

// Funciones para cargar productos desde Instagram
const fetchProductFeedDemo = async () => {
  await new Promise(resolve => setTimeout(resolve, 800))
  
  // Posts reales de joyeria_tu_estilo_
  const mockProducts = [
    {
      id: 'prod-1',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/468462289_18063358789494756_2747911920248602550_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=103&_nc_ohc=_uq1O3qf7DMAX9XZ4bJ&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfAk0UXn41-CgLy2x-mf_oYlp9jQFnPaJg5oQYCXSA_YWA&oe=667A5C76&_nc_sid=10d13b',
      caption: 'Cuando la elegancia y la calidad se unen, nace la perfección ✨💎 #joyeria #anillos #plata #calidad',
      permalink: 'https://www.instagram.com/p/C9oKWtgOTvJ/',
      category: 'Anillos',
      timestamp: new Date().toISOString()
    },
    {
      id: 'prod-2',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/473075592_936376264855538_4257053073772875337_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=106&_nc_ohc=nF-gMtXGQu0AX9ZnA5-&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfAX1wgHH-0ZNsRQVQ-_N67_hb3kx4JYNVxBQX-vN_s_KQ&oe=667B7A4E&_nc_sid=10d13b',
      caption: 'Eres única, tu joyería también debería serlo ✨ Encuentra las piezas perfectas para complementar tu estilo en nuestra colección 🛍️✨ #joyeria #aretes #pulseras',
      permalink: 'https://www.instagram.com/p/C9oImOHunCw/',
      category: 'Aretes',
      timestamp: new Date().toISOString()
    },
    {
      id: 'prod-3',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/472956022_1087826629284458_5518280462409203538_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=101&_nc_ohc=wrbGqKt7hGQAX8gsjqQ&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfCYo_l6rJxu-R3YeT23HN3oo4e63L2c9-G0pOOnnLpOyg&oe=667B09B2&_nc_sid=10d13b',
      caption: '¡Prepárate para deslumbrar! 💫 Nuestras joyas de fantasía fina están diseñadas para que te sientas hermosa en cada ocasión 💕 #joyeria #collares #aretes',
      permalink: 'https://www.instagram.com/p/C9oHFdVOejp/',
      category: 'Collares',
      timestamp: new Date().toISOString()
    },
    {
      id: 'prod-4',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/464483243_382747611172249_8485764904341694089_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=108&_nc_ohc=Asg2XnCkqB0AX9jQcB2&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfAPEQ2S1PCjxz3Fj-8eDzjEXjvDvnOaYjSuL20JYq77gw&oe=667C0B61&_nc_sid=10d13b',
      caption: 'Nuestras pulseras son el complemento perfecto para tu outfit diario ✨ Encuentra tu estilo con nosotros 💎 #pulseras #joyeria #accesorios',
      permalink: 'https://www.instagram.com/p/C9oFqMXOjgB/',
      category: 'Pulseras',
      timestamp: new Date().toISOString()
    },
    {
      id: 'prod-5',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/467673060_853835366601048_3318456409211387233_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=106&_nc_ohc=S4pJd12hD6gAX8gCn-k&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfAf1-uQs8Nm2gFAKElMRhd-SsQ_9ZigSCx_mY4jD6rLig&oe=667CA24A&_nc_sid=10d13b',
      caption: 'Un toque de elegancia para cada día ✨ Descubre nuestras piezas exclusivas de joyería fina y semi-fina 💕 #joyeria #elegancia #estilo',
      permalink: 'https://www.instagram.com/p/C9oFPNAO6qO/',
      category: 'Joyería Fina',
      timestamp: new Date().toISOString()
    },
    {
      id: 'prod-6',
      media_type: 'IMAGE',
      media_url: 'https://scontent-mia3-1.cdninstagram.com/v/t51.29350-15/472873566_1081636586598407_5242147868504834431_n.jpg?stp=dst-jpg_e35_p1080x1080&_nc_ht=scontent-mia3-1.cdninstagram.com&_nc_cat=101&_nc_ohc=Kd_uv_vYAV0AX94Gvmy&edm=APs17CUBAAAA&ccb=7-5&oh=00_AfDgYjcJ2kZI88-EiTWcuVZa_Fp_F7qMkWLqaK9UYWB_JQ&oe=667A5AED&_nc_sid=10d13b',
      caption: 'Cada joya cuenta una historia, ¿cuál será la tuya? 💫 Visita nuestra tienda y encuentra piezas que hablarán por ti ✨ #joyeria #accesorios #estilo',
      permalink: 'https://www.instagram.com/p/C9oDyZRuNx7/',
      category: 'Accesorios',
      timestamp: new Date().toISOString()
    }
  ]

  return {
    data: mockProducts,
    paging: {
      cursors: {
        after: 'mock_token_1'
      },
      next: true
    }
  }
}

// Cargar productos de Instagram
const loadProductPosts = async () => {
  try {
    loadingProducts.value = true
    errorProducts.value = null
    
    // Obtener los posts de productos de nuestra función de demostración
    const productFeed = await fetchProductFeedDemo()
    productPosts.value = productFeed.data
    
  } catch (e) {
    errorProducts.value = 'No se pudieron cargar los productos. Por favor, intenta de nuevo más tarde.'
    console.error('Error loading product posts:', e)
  } finally {
    loadingProducts.value = false
  }
}

// Generar título del producto a partir del caption
const getProductTitle = (product) => {
  // Extraer la primera frase del caption o limitarlo a cierta cantidad de palabras
  const firstSentence = product.caption.split(/[.!?]/)[0]
  if (firstSentence.length < 50) {
    return firstSentence
  } else {
    // Si la primera frase es muy larga, limitamos a 5-7 palabras
    return product.caption.split(' ').slice(0, 6).join(' ') + '...'
  }
}

// Extraer categoría del producto
const getProductCategory = (product) => {
  if (product.category) {
    return product.category
  }
  
  // Intentar extraer categoría de los hashtags
  const hashtagMatch = product.caption.match(/#(\w+)/g)
  if (hashtagMatch) {
    const categories = ['Anillos', 'Aretes', 'Collares', 'Pulseras', 'Joyeria', 'Accesorios']
    for (const tag of hashtagMatch) {
      const cleanTag = tag.substring(1) // Remover el #
      const matchedCategory = categories.find(cat => 
        cleanTag.toLowerCase() === cat.toLowerCase() || 
        cleanTag.toLowerCase().includes(cat.toLowerCase())
      )
      if (matchedCategory) return matchedCategory
    }
    // Si no encontramos una categoría específica, devolvemos el primer hashtag sin el #
    return hashtagMatch[0].substring(1)
  }
  
  return 'Joyería'
}

// Generar enlace de WhatsApp para productos
const getWhatsAppLinkForProduct = (product) => {
  const message = encodeURIComponent(
    `Hola, estoy interesado en este producto: ${product.permalink}\n¿Está disponible?`
  )
  return `https://wa.me/${whatsAppNumber}?text=${message}`
}

// Generar enlace de WhatsApp para consultas generales
const getWhatsAppLink = (post) => {
  const message = encodeURIComponent(
    `Hola, me gustaría más información sobre este artículo: ${post.permalink}`
  )
  return `https://wa.me/${whatsAppNumber}?text=${message}`
}

// Generar enlace de WhatsApp para ubicación
const getDirectionsWhatsAppLink = () => {
  const message = encodeURIComponent(
    `Hola, me gustaría saber cómo llegar a su tienda en Moncagua frente a las piscinas El Capulín. ¿Me pueden dar indicaciones?`
  )
  return `https://wa.me/${whatsAppNumber}?text=${message}`
}

// Manejar envío del formulario de contacto
const handleSubmit = () => {
  if (!isFormValid.value) {
    formError.value = 'Por favor, completa todos los campos requeridos correctamente';
    return;
  }
  
  isSubmitting.value = true;
  formError.value = null;
  
  // Simulamos el envío del formulario con un retraso
  setTimeout(() => {
    try {
      // Aquí iría la lógica para enviar el formulario a un backend real
      console.log('Formulario enviado:', contactForm.value);
      
      // Simulamos un envío exitoso
      formSuccess.value = true;
      
      // Reseteamos el formulario
      contactForm.value = {
        name: '',
        email: '',
        phone: '',
        message: '',
        consent: false
      };
    } catch (error) {
      formError.value = 'Ocurrió un error al enviar tu mensaje. Por favor, intenta nuevamente.';
      console.error('Error al enviar formulario:', error);
    } finally {
      isSubmitting.value = false;
    }
  }, 1500);
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
  handleScroll() // Verificar scroll inicial
  loadInstagramPosts() // Cargar posts de Instagram
  loadProductPosts() // Cargar posts de productos
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleScroll)
  document.body.classList.remove('overflow-hidden')
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

/* Animación de carga */
@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

/* Transiciones del menú */
.menu-icon-enter-active,
.menu-icon-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.menu-icon-enter-from,
.menu-icon-leave-to {
  opacity: 0;
  transform: scale(0.95);
}

/* Estilos para el título en el header */
.text-title {
  font-size: 1.8rem;
  font-weight: 600;
  background: linear-gradient(to right, #ff69b4, #ff1493);
  -webkit-background-clip: text;
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
</style>
