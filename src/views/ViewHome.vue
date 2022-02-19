<template>
  <div>
    <v-btn v-on:click="clickStart">NFC読取</v-btn>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from '@vue/composition-api'

type State = {
  serialNumber: string
  text: string
}
export default defineComponent({
  components: {},
  setup() {
    const state = reactive<State>({
      serialNumber: '',
      text: '',
    })

    const clickStart = async () => {
      try {
        const NDEFReader = window.NDEFReader
        // eslint-disable-next-line no-undef
        const reader = new NDEFReader()
        await reader.scan()
        reader.addEventListener('error', () => {
          console.log('Error')
        })

        reader.addEventListener('reading', (value: any) => {
          const message = value.message
          const serialNumber = value.serialNumber

          console.log(`> Serial Number: ${serialNumber}`)
          console.log(message)
          state.serialNumber = serialNumber
          const record = message.records[0]
          const { data, encoding, recordType } = record
          if (recordType === 'text') {
            const textDecoder = new TextDecoder(encoding)
            state.text = textDecoder.decode(data)
          }
        })
      } catch (error) {
        console.error(error)
      }
    }
    return {
      ...toRefs(state),
      clickStart,
    }
  },
})
</script>

<style lang="scss" scoped></style>
