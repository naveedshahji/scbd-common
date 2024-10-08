<template>
  <button    
    class="scbd-common select-file-button btn btn-primary"
    type="button"
    :id="$attrs.id"
    @click.prevent.stop="selectFile()">  
      <slot name="default" ></slot>
  </button> 
</template>

<script setup lang="ts">
const emit = defineEmits(['onFileSelected']);
  const props = defineProps({
    multiple: { type: Boolean, required: false, default: false },
    accept: { type: Array as () => string[], required: false, default: () => ['*/*'] }
  }) 
  
  // if not retuning
  const selectFile = (): void =>{   
    const fileInput: any = document.createElement('input');
   
    fileInput.type = 'file';
    fileInput.style.display = 'none';
  
    if (props.multiple) fileInput.setAttribute('multiple', props.multiple);
    if (props.accept && !props.accept?.includes('*/*')) fileInput.setAttribute('accept', props.accept?.join(', '));
  
    fileInput.addEventListener('click', ($event: Event) => $event.stopPropagation());    
    // fileInput.addEventListener('change', ({ target }) => { 
    //   const files = [...target.files]; 
    //   if (props.multiple)
    //     emit('onFileSelected', files);   
    //   else 
    //     emit('onFileSelected', files[0]);   
      
    // }) 
    fileInput.addEventListener('change', ($event: Event) => { 
    const target = $event.target as HTMLInputElement;
    // handle null vales here too  
    const files = [...(target.files || [])];
    if (props.multiple) {
      emit('onFileSelected', files);   
    } else {
      emit('onFileSelected', files[0]);   
    }
  });

    fileInput.click();   
  }
</script>