<template>
  <!-- Sección de Posts de Instagram -->
  <section class="py-16 bg-gradient-to-br from-pink-50 to-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="text-center mb-12">
        <h2 class="text-3xl md:text-4xl font-bold mb-4">Síguenos en Instagram</h2>
        <p class="text-gray-600 max-w-3xl mx-auto">
          Mantente al día con nuestras últimas novedades y productos
        </p>
      </div>

      <!-- Grid de Posts de Instagram -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 gap-8">
        <!-- Instagram Posts -->
        <template v-for="post in instagramPosts" :key="post.url">
          <div class="instagram-post-container">
            <blockquote
              class="instagram-media"
              data-instgrm-captioned
              :data-instgrm-permalink="post.url"
              data-instgrm-version="12"
              :style="{
                background: '#FFF',
                border: 0,
                borderRadius: '15px',
                boxShadow: '0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15)',
                margin: '1px',
                maxWidth: '540px',
                minWidth: '326px',
                padding: 0,
                width: '99.375%'
              }"
            >
            </blockquote>
          </div>
        </template>
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
          Seguir en Instagram
        </a>
      </div>
    </div>
  </section>

  <!-- Sección de Ubicación -->
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  name: 'Navbar',
  
  setup() {
    const instagramPosts = ref([
      {
        url: 'https://www.instagram.com/p/C3SvzQerlh_/'

      },
      {
        url: 'https://www.instagram.com/p/CxL56E8v-Zt/',

      },
      {
        url: 'https://www.instagram.com/p/CxYbVZvLBHS/',

      },
      {
        url: 'https://www.instagram.com/p/CwybZInv4Dr/',

      }
    ]);

    const reloadInstagramEmbeds = () => {
      if (window.instgrm) {
        window.instgrm.Embeds.process();
      }
    };

    onMounted(() => {
      // Cargar el script de Instagram cuando el componente se monta
      const script = document.createElement('script');
      script.async = true;
      script.src = '//www.instagram.com/embed.js';
      document.body.appendChild(script);
      
      // Procesar los embeds cuando el script se carga
      script.onload = () => {
        reloadInstagramEmbeds();
      };
    });

    return {
      instagramPosts,
      reloadInstagramEmbeds
    };
  }
};
</script>

<style scoped>
.instagram-post-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}
</style>