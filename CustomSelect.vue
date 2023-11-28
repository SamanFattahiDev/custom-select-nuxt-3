<template>
  <div :class="customClasses"
       class="w-full relative   ">
    <div v-if="isDropdownOpen" class="fixed w-full h-full top-0 right-0 bg-transparent z-[1]"
         @click="isDropdownOpen = false"></div>
    <div
        class="bg-white !z-[2]  relative p-1 border border-gray-200 rounded cursor-text flex items-center justify-between">
      <span v-if="selectedItem" class="px-4">{{ selectedItem[label] }}</span>
      <input v-model="searchQuery" class=" w-11/12" type="text" @click="handleDropdown">
      <div class="icons flex items-center gap-1" @click="handleDropdown">
        <svg v-if="selectedItem &&!isDropdownOpen" class="w-5 h-5 cursor-pointer fill-gray-400" viewBox="0 0 24 24"
             xmlns="http://www.w3.org/2000/svg" @click.stop="removeItem">
          <path
              d="M12.0007 10.5865L16.9504 5.63672L18.3646 7.05093L13.4149 12.0007L18.3646 16.9504L16.9504 18.3646L12.0007 13.4149L7.05093 18.3646L5.63672 16.9504L10.5865 12.0007L5.63672 7.05093L7.05093 5.63672L12.0007 10.5865Z"></path>
        </svg>
        <svg :class="{'transform rotate-180':isDropdownOpen}" class="w-5 h-5 transition-all delay-100 fill-gray-400"
             viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
          <path
              d="M11.9997 13.1714L16.9495 8.22168L18.3637 9.63589L11.9997 15.9999L5.63574 9.63589L7.04996 8.22168L11.9997 13.1714Z"></path>
        </svg>
      </div>
    </div>
    <transition name="slide-down">
      <div v-if="isDropdownOpen"
           class=" bg-white rounded absolute flex flex-col max-h-[15rem]  overflow-y-auto  -bottom-22 z-[2] right-0 w-full shadow">
        <template v-if="filteredOptions.length>0">
          <div v-for="(item,idx) in filteredOptions"
               class="hover:bg-primary cursor-pointer transition-all hover:text-white p-3"
               @click="setItem(item)">
            <slot :data="item" name="option">{{ item[label] }}</slot>
          </div>
        </template>
        <div v-else class="w-full flex items-center p-2 justify-center">
          {{ emptyText }}
        </div>

      </div>
    </transition>
  </div>
</template>

<script lang="ts" setup>
const emits = defineEmits([
  'update:modelValue'
])
const props = defineProps({
  modelValue: {},
  emptyText: {
    type: String,
    default: 'No matching options!',
    required: false,
  },
  extract: {
    type: String,
    default: '',
    required: false,
  },
  options: {
    type: Array,
    default: [],
    required: true,
  },
  label: {
    type: String,
    default: '',
    required: true,
  },
  customClasses: {
    type: String,
    default: '',
    required: false,
  },
  toggleAble: {
    type: Boolean,
    required: false,
    default: false,
  }
})

let isDropdownOpen = ref<Boolean>(false)
let searchQuery = ref<string>('')
let selectedItem = ref<any>(null)
let filteredOptions = computed(() => {
  return props.options.filter((el: any) => {
    return el[props.label].match(searchQuery.value)
  })
})

function setItem(item: any) {
  selectedItem.value = item
  emits('update:modelValue', props.extract ? selectedItem.value[props.extract] : selectedItem.value)
  isDropdownOpen.value = false
}

function handleDropdown() {
  if (props.toggleAble) {
    isDropdownOpen.value = !isDropdownOpen.value
  } else {
    isDropdownOpen.value = true
  }
}

function removeItem() {
  selectedItem.value = null
  emits('update:modelValue', props.extract ? selectedItem.value[props.extract] : selectedItem.value)

}
</script>

<style scoped>
.slide-down-enter-active {
  animation: slideDownAnm 0.2s ease-in-out;
}

.slide-down-leave-active {
  animation: slideDownAnm reverse 0.2s ease-in-out;
}

@keyframes slideDownAnm {
  0% {
    opacity: 0;
    /*transform: translatey(-100%);*/
  }
  100% {
    opacity: 1;
    /*transform: translateX(0);*/
  }
}

</style>
