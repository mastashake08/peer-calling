<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-heading">Call Component</div>

                    <div class="panel-body">
                        Place a call by putting the caller-id in the box below and
                        hit the call button. Your caller id is {{user.username}}
                        <br>
                        <div class="form-group">
                        <input type="text" class="form-control" id="caller_id" v-model="caller_id">
                        <button class="btn btn-sm" v-on:click="placeCall">Place Call</button>
                      </div>
                      <audio id="call" autoplay controls>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        },
        props:  ['userObject'],
        created(){
          this.user = JSON.parse(this.userObject);
          this.peer = new Peer(this.user.username,{host: 'localhost', port: 9000, path: '/peer'});
          var getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
          this.peer.on('call',this.answerCall);
        },
        data(){
          return{
            peer: {},
            user: {},
            caller_id: ''
          }
        },
        methods:{
          placeCall: function(){
            var getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
            getUserMedia({video: false, audio: true}, function(stream) {
              var call = this.peer.call(this.caller_id, stream);
              call.on('stream', function(remoteStream) {
                // Show stream in some video/canvas element.
                var audio = document.querySelector('audio');

              //inserting our stream to the video tag
              audio.src = window.URL.createObjectURL(remoteStream);
              });
            }, function(err) {
              console.log('Failed to get local stream' ,err);
            });
          },
          answerCall: function(call){
            getUserMedia({video: false, audio: true}, function(stream) {
             call.answer(stream); // Answer the call with an A/V stream.
             call.on('stream', function(remoteStream) {
               // Show stream in some video/canvas element.
               var audio = document.querySelector('audio');

             //inserting our stream to the video tag
             audio.src = window.URL.createObjectURL(remoteStream);
             });
           }, function(err) {
             console.log('Failed to get local stream' ,err);
           });
          }
        }
    }
</script>
