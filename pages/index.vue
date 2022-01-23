<template>
  <div>
    <div class="py-16 bg-gray-50 overflow-hidden lg:py-24">
      <div class="relative max-w-xl mx-auto px-4 sm:px-6 lg:px-8 lg:max-w-7xl">
        <svg class="hidden lg:block absolute left-full transform -translate-x-1/2 -translate-y-1/4" width="404" height="784" fill="none" viewBox="0 0 404 784" aria-hidden="true">
          <defs>
            <pattern id="b1e6e422-73f8-40a6-b5d9-c8586e37e0e7" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
              <rect x="0" y="0" width="4" height="4" class="text-gray-200" fill="currentColor" />
            </pattern>
          </defs>
          <rect width="404" height="784" fill="url(#b1e6e422-73f8-40a6-b5d9-c8586e37e0e7)" />
        </svg>

        <div class="relative">
          <h2 class="text-center text-3xl leading-8 font-extrabold tracking-tight text-gray-900 sm:text-4xl">
            Converting 3D models to 360 animated models (GIFS)
          </h2>
          <p class="mt-4 max-w-3xl mx-auto text-center text-xl text-gray-500">
            Feel free to upload or select a 3D model for animation
          </p>
        </div>

        <div class="min-h-full flex flex-col justify-center py-12 sm:px-6 lg:px-8">

          <div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
            <div class="bg-white py-8 px-4 shadow sm:rounded-lg sm:px-10">
              <form class="space-y-6" @submit.prevent="upload">

                <div>
                  <label for="model" class="block text-sm font-medium text-gray-700">
                    3D Model (Only .glb, .gltz, .fbxz
                  </label>
                  <div class="mt-1">
                    <input 
                      @change="handleFile"
                      id="model" 
                      name="model" 
                      type="file" 
                      accept=".glb, .gltz, .fbxz"
                      required 
                      class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    />
                  </div>
                </div>

                <div>
                  <p class="text-muted text-sm text-center" v-if="uploading">Uploading... </p>
                  <button v-else type="submit" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                    Upload
                  </button>
                </div>
              </form>

              <div class="mt-6">
                <div class="relative">
                  <div class="absolute inset-0 flex items-center">
                    <div class="w-full border-t border-gray-300"></div>
                  </div>
                  <div class="relative flex justify-center text-sm">
                    <span class="px-2 bg-white text-gray-500">
                      Or view
                    </span>
                  </div>
                </div>

                <div class="mt-6">
                  <div class="my-2" v-for="sample in samples" :key="sample.publicId">
                    <button @click="display=sample" class="w-full inline-flex justify-center py-2 px-4 border border-gray-300 rounded-md shadow-sm bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
                      <span>{{sample.name}}</span>
                    </button>
                  </div>

                </div>
              </div>
            </div>
          </div>
        </div>

      <div v-if="display" class="text-center">
        <cld-image :alt="`${display.name} loading`" class="mx-auto" :public-id="display.publicId" >
          <cld-transformation delay="10" flags="animated" fetch-format="gif">
          </cld-transformation>
          <cld-transformation height="200" width="400" crop="fill">
          </cld-transformation>
          <cld-transformation background="#3448C5">
          </cld-transformation>
        </cld-image>
        <p class="text-md w-1/3 mx-auto my-10">
          {{ display.credit }}
        </p>
      </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return {
      model:null,
      uploading:false,
      display: null ,
      samples:[
        {
          name: "Cloudinary logo",
          credit: "Cloudinary logo by Cloudinary",
          publicId: 'nuxtjs-3d-model-to-360/CldLogo3D',
        },
        {
          name:"18th century oilсan",
          credit: "18th century oilсan (https://skfb.ly/oqKtP) by motionpix is licensed under Creative Commons Attribution (http://creativecommons.org/licenses/by/4.0/).",
          publicId:"nuxtjs-3d-model-to-360/OilCan",
        },
        {
          name:"Pencil",
          credit: "Pencil (https://skfb.ly/o7GUU) by jackie glade is licensed under Creative Commons Attribution (http://creativecommons.org/licenses/by/4.0/).",
          publicId:"nuxtjs-3d-model-to-360/pencil",
        }
      ]
    };
  },
  methods: {
    async handleFile(e) {
      this.model = e.target.files[0];
    },
    async readData(f) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onloadend = () => resolve(reader.result);
        reader.readAsDataURL(f);
      });
    },
    async upload() {
      this.uploading = true;
      const modelData = await this.readData(this.model);
      const cloudinaryModel = await this.$cloudinary.upload(modelData, {
        upload_preset: "default-preset",
        folder: "nuxtjs-3d-model-to-360",
      });
      this.display = {
        publicId: cloudinaryModel.public_id
      }
      this.uploading = false;
    },
  },
}
</script>
