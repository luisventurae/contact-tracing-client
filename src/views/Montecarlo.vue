<template>
    <div class="montecarlo">
        <canvas id="lienzo" ref="lienzo"></canvas>
    </div>
</template>

<script>
export default {
    name: 'Montecarlo',
    data: () => ({
        cv: null,
        ctx: null,
        radius: 50,
        cx: 50,
        cy: 50,
        circleColor: 'blue',
        circleSelectedColor: 'red',
    }),
    mounted() {
        this.load()
    },
    methods: {
        load() {
            this.cv = this.$refs.lienzo
            this.cv = document.getElementById('lienzo')
            this.ctx = this.cv.getContext('2d')
            this.drawCircle(this.circleColor)
        },
        drawCircle(color) {
            this.ctx.beginPath()
            this.ctx.arc(this.cx, this.cy, this.radius, Math.PI*2, 0)
            this.ctx.fillStyle = color
            this.ctx.fill()
        },
    },
}
</script>

<style lang="sass" scoped>
.montecarlo 
    canvas
        width: 640px
        // height: 480px
        // background: red
</style>