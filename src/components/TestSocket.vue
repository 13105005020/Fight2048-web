<template>
  <div class="test">

  </div>
</template>
<script>
export default {
  name: 'test',
  data () {
    return {
      websock: null
    }
  },
  created () {
    this.initWebSocket()
  },
  destroyed () {
    this.websock.close()
  },
  methods: {
    initWebSocket () {
      const wsuri = 'ws://127.0.0.1:3653'
      this.websock = new WebSocket(wsuri)
      this.websock.binaryType = 'arraybuffer'
      this.websock.onmessage = this.websocketonmessage
      this.websock.onopen = this.websocketonopen
      this.websock.onerror = this.websocketonerror
      this.websock.onclose = this.websocketclose
    },
    websocketonopen () {
      let JoinRoom = {
        'JoinRoom': {
          'Type': 1
        }
      }
      this.websocketsend(JSON.stringify(JoinRoom))
    },
    websocketonerror () {
      this.initWebSocket()
    },
    websocketonmessage (e) {
      var data = new Uint8Array(e.data)
      const redata = JSON.parse(Utf8ArrayToStr(data))
      console.log(redata.Hello.Name)
    },
    websocketsend (Data) {
      this.websock.send(Data)
    },
    websocketclose (e) {
      console.log('断开连接', e)
    }
  }
}

function Utf8ArrayToStr (array) {
  var out, i, len, c
  var char2, char3
  out = ''
  len = array.length
  i = 0
  while (i < len) {
    c = array[i++]
    switch (c >> 4) {
      case 0: case 1: case 2: case 3: case 4: case 5: case 6: case 7:
      // 0xxxxxxx
        out += String.fromCharCode(c)
        break
      case 12: case 13:
      // 110x xxxx 10xx xxxx
        char2 = array[i++]
        out += String.fromCharCode(((c & 0x1F) << 6) | (char2 & 0x3F))
        break
      case 14:
      // 1110 xxxx 10xx xxxx 10xx xxxx
        char2 = array[i++]
        char3 = array[i++]
        out += String.fromCharCode(((c & 0x0F) << 12) |
        ((char2 & 0x3F) << 6) |
        ((char3 & 0x3F) << 0))
    }
  }
  return out
}
</script>
