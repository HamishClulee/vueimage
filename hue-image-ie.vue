<template>
    <div class="image-con">

        <div
            :id="`img-${hash}`"
            class="base"
            :class="status === 'loaded' ? 'show' : 'hide'"
        ></div>

        <svg
            :id="`svg-${hash}`"
            class="base" width="100%"
            :height="svgheight"
            :class="status !== 'loaded' ? 'show' : 'hide'"
        >
            <rect
                width="100%"
                :height="svgheight"
                :style="{ 
                    fill: perc2color(Math.floor(hash / 10000000)),
                    stroke:'none'
                }"
            />
        </svg>

    </div>
</template>

<script>
// TODO:
// - allow user to set background color of svg tiles using a prop
//
// - possible choice between svg background and a slot
//
// - implement error behavior, if src incorrect or server unresponsive, display ddfault 404, or slot 404
//
// - SEO behavior considerations - alt tags, make sure alt tags are rendered before crawl
// 
// - The h x w ratio for the svg to be settable via a prop with solid defaults
//
export default {
    name: 'vueimage',
    props: {
        imgsrc: String,
        srcmap: Object,
    },
    data () {
        return {
            image: null,
            status: 'loading',
            loaderror: false,
            hash: null,
            el: null,
            hidesvg: false,
            attr: '',
            eltop: null,
            windowtop: null,
            scrollticking: false,
            scrollloc: 0,
            ratio: null,
            svgel: null,
            svgheight: 0,
            scrolltrigger: 2000,
        }
    },
    created() {

        this.hash = Math.floor((Math.random() * 99999999) + 1)
        this.scrollloc = window.scrollY
        
    },
    mounted() {

        this.svgel = document.getElementById(`svg-${this.hash}`)

        this.svgheight = Math.floor((this.svgel.getBoundingClientRect().right - this.svgel.getBoundingClientRect().left) * 0.66)

        this.windowtop = Math.abs(document.body.getBoundingClientRect().top)

        window.addEventListener('scroll', this.scrolling)
    
        this.windowpos = Math.abs(document.body.getBoundingClientRect().top)

        this.attr = this.$el.firstElementChild.attributes[0].nodeName
        this.el = document.getElementById(`img-${this.hash}`)

        this.$nextTick(() => { 
            this.eltop = this.$el.getBoundingClientRect().top 
            if (this.imgsrc && this.el && this.shouldinit) this.createLoader()
            else if (!this.imgsrc || !this.el) this.handleError()
        })
    },
    methods: {
        scrolling () {
            this.scrollloc = window.scrollY
            if (!this.scrollticking) {
                window.requestAnimationFrame(() => {
                    if (this.status !== 'loaded' && (this.scrolltrigger + this.scrollloc > this.eltop)) {
                        this.createLoader()
                    }
                    this.scrollticking = false
                })
                this.scrollticking = true
            }
        },
        perc2color(perc) {
            let h = Math.round(510 - 5.10 * perc) * 0x10000 + 62965
            return '#' + ('000000' + h.toString(16)).slice(-6)
        },
        handleError() {
            this.loaderror = true
            this.status = 'error'
        },
        createLoader() {
            this.image = new Image()
            this.image.onload = this.handleLoad
            this.image.onerror = this.handleError
            this.image.src = this.imgsrc
            this.image.setAttribute(this.attr, '')
            this.el.appendChild(this.image)
            if (this.srcmap) {
                this.applysrcset()
            }
            this.status = 'loaded'
        },
        applysrcset() {
            this.image.setAttribute('srcset', `${this.srcmap['small']} 480w, ${this.srcmap['medium']} 1280w, ${this.srcmap['large']} 1980w`)
        },
        handleLoad() {
            // TODO unsure that this even needs impl
        }
    },
    computed: {
        shouldinit() {
            return this.eltop < this.scrolltrigger
        },
    },
    beforeDestroy() {
        window.removeEventListener('scroll', this.scrolling)
    },
}
</script>
<style lang="sass" scoped>
.base
    opacity: 1
    transition: opacity 0.5s ease, visibility 0.5s ease
    object-fit: fill
    width: 100%
.show
    visibility: visible
    opacity: 1
    transition: opacity 0.5s ease, visibility 0.5s ease
.hide
    visibility: hidden
    opacity: 0
    height: 0
    transition: opacity 0.5s ease, visibility 0.5s ease
img
    width: 100%
    object-fit: fill
</style>
