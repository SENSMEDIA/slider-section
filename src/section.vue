<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="section-hello-world">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl"></wwSectionEditMenu>
        <!-- wwManager:end -->

        <!-- This is the background of the section -->
        <wwObject class="background" v-bind:ww-object="section.data.background" ww-category="background">
        </wwObject>

        <div class="content-container">
            <!-- This is a simple WeWeb object which can be anything in the editor -->

            <!-- This is another WeWeb object that we initiaze as an image -->
            <div class="left-container">
                <div v-for="(item, index) in section.data.contentList"
                     v-bind:class="[item.mainImage ? 'show main ': '', 'header header-' + index]">
                    <wwObject class="title" v-if="item.title"
                              v-bind:ww-object="item.title"></wwObject>
                    <wwObject class="subtitle" v-if="item.subtitle"
                              v-bind:ww-object="item.subtitle"></wwObject>
                </div>
                <div class="icons-list">
                    <div class="points">
                        <wwObject v-for="(item, index) in section.data.contentList"
                                  v-bind:class="[item.isActive ? 'current' : '', 'dot icon-' + index]"
                                  v-bind:ww-object="item.icon"
                                  v-on:click="currentSlide(index + 1)"></wwObject>
                    </div>
                    <wwObject class="icon" v-bind:ww-object="section.data.iconLeft"
                              v-on:click="nextSlide(-1)"></wwObject>
                    <wwObject class="icon" v-bind:ww-object="section.data.iconRight"
                              v-on:click="nextSlide(1)"></wwObject>
                </div>
            </div>
            <!-- This is a content block, a list of objects, it will add the little blue "plus" around the objects in the editor -->
            <div class="right-container">
                <wwObject v-for="(item, index) in section.data.contentList" ww-default-ratio="80"
                          v-bind:class="[item.isActive ? 'active' : '', item.mainImage ? 'main-image' : '', 'image all-images image-' + index]"
                          v-bind:ww-object="item.image"></wwObject>
            </div>
        </div>
        <wwObject class="icons" v-bind:ww-object="section.data.arrowIcon"></wwObject>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
    export default {
        name: '__COMPONENT_NAME__',
        props: {
            // The section controller object is passed. You can access it anytime
            sectionCtrl: Object
        },
        data() {
            return {
                slideIndex: 0,
                zIndex: 1
            };
        },
        computed: {
            // The section object contains all the info and data about the section
            // Use it as you like !
            section() {
                return this.sectionCtrl.get();
            }
        },
        created() {
            // Initialize the data once the section is created in the DOM
            this.init();
            this.slideIndex = 0;
        },
        mounted() {
            this.currentSlide(1);
        },
        methods: {
            init() {
                // Initialize section data
                let needUpdate = false;

                // We will only save the data in this.section.data
                // So you need to put in there all the data of you WeWeb objects
                this.section.data = this.section.data || {};

                // Initialize WeWeb objects that are in the html template up there
                if (!this.section.data.background) {
                    this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color' });
                    needUpdate = true;
                }

                if (!this.section.data.arrowIcon) {
                    this.section.data.arrowIcon = wwLib.wwObject.getDefault({
                        type: 'ww-icon', data: {
                            icon: 'fas fa-arrow-down',
                            style: {
                                size: 24
                            }
                        }
                    });
                    needUpdate = true;
                }

                if (!this.section.data.iconLeft) {
                    this.section.data.iconLeft = wwLib.wwObject.getDefault({
                        type: 'ww-icon', data: {
                            icon: 'fas fa-angle-left',
                            style: {
                                size: 24
                            }
                        }
                    });
                    needUpdate = true;
                }
                if (!this.section.data.iconRight) {
                    this.section.data.iconRight = wwLib.wwObject.getDefault({
                        type: 'ww-icon', data: {
                            icon: 'fas fa-angle-right',
                            style: {
                                size: 24,
                                paddingLeft: 20
                            }
                        }
                    });
                    needUpdate = true;
                }

                if (!this.section.data.contentList) {
                    this.section.data.contentList = [
                        {
                            image: wwLib.wwObject.getDefault({
                                type: 'ww-image',
                                data: {
                                    url: 'https://weweb.twic.pics/designs/436/sections/union-new.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.7,
                                    style: {
                                        // minWidth: 640,
                                        ratio: 1
                                    }

                                }
                            }),
                            icon: wwLib.wwObject.getDefault({
                                type: 'ww-icon', data: {
                                    icon: 'fas fa-circle',
                                    style: {
                                        size: 12,
                                        fontSize: 12,
                                        borderWidth: 0
                                    }
                                }
                            }),
                            title: wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: {
                                        en: 'Peers amongst entrepreneurs'
                                    }
                                }
                            }),
                            mainImage: true,
                            isActive: true
                        },
                        {
                            image: wwLib.wwObject.getDefault({
                                type: 'ww-image',
                                data: {
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_5.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.556,
                                    position: { x: -12.2, y: 4.7 },
                                    style: {
                                        // minWidth: 510,
                                        ratio: 1,
                                        borderRadius: 100
                                    }
                                }
                            }),
                            icon: wwLib.wwObject.getDefault({
                                type: 'ww-icon', data: {
                                    icon: 'fas fa-circle',
                                    style: {
                                        size: 12,
                                        fontSize: 12,
                                        color: '#343C38',
                                        borderWidth: 0
                                    }
                                }
                            }),
                            title: wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: {
                                        en: 'The world was over subscribed, but I wanted to have 42CAP due to their entrepreneurial experience.'
                                    }
                                }
                            }),
                            subtitle: wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: {
                                        en: 'Inigo Juantegui • CEO & Founder OnTruck'
                                    }
                                }
                            }),
                            isActive: false
                        },
                        {
                            image: wwLib.wwObject.getDefault({
                                type: 'ww-image',
                                data: {
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_3.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.6,
                                    position: { x: 7.7, y: 1.5 },
                                    style: {
                                        borderRadius: 100,
                                        ratio: 1,
                                        // minWidth: 540
                                    }
                                }
                            }),
                            icon: wwLib.wwObject.getDefault({
                                type: 'ww-icon', data: {
                                    icon: 'fas fa-circle',
                                    style: {
                                        size: 12,
                                        fontSize: 12,
                                        borderWidth: 0
                                    }
                                }
                            }),
                            title: wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: {
                                        en: '42CAP had a conviction in the Gig Economy space, which intrigued us.'
                                    }
                                }
                            }),
                            subtitle: wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: {
                                        en: 'Nicolas Rebout • CEO & Founder Shine'
                                    }
                                }
                            }),
                            isActive: false
                        }
                    ];
                    needUpdate = true;
                }

                if (needUpdate) {
                    this.sectionCtrl.update(this.section);
                }
            },
            nextSlide(index) {
                this.showSlides(this.slideIndex += index);
            },
            currentSlide(n) {
                console.log(n);
                this.showSlides(this.slideIndex = n);
            },
            showSlides(n) {
                let i;
                let slides = document.getElementsByClassName('all-images');
                let dots = document.getElementsByClassName('dot');
                let headers = document.getElementsByClassName('header');

                if (n > slides.length) {
                    this.slideIndex = 1;
                }

                if (n < 1) {
                    this.slideIndex = slides.length;
                }
                for (i = 0; i < headers.length; i++) {
                    if (headers[i].classList.contains('show')) {
                        headers[i].classList.remove('show');
                    }
                }

                for (i = 0; i < slides.length; i++) {
                    if (slides[i].classList.contains('active')) {
                        slides[i].classList.remove('active');
                    }
                }

                for (i = 0; i < dots.length; i++) {
                    if (dots[i].classList.contains('current')) {
                        dots[i].classList.remove('current');
                    }
                }

                for (i = 0; i < this.section.data.contentList.length; i++) {
                    this.section.data.contentList[i].isActive = false;
                }

                slides[this.slideIndex - 1].classList.add('active');
                slides[this.slideIndex - 1].style.zIndex = (this.zIndex++).toString();
                dots[this.slideIndex - 1].classList.add('current');
                headers[this.slideIndex - 1].classList.add('show');
                this.section.data.contentList[this.slideIndex - 1].isActive = true;
            }
        }
    };
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang="scss" scoped>
    .section-hello-world {
        &::v-deep {

            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }

            .content-container {
                position: relative;
                /*max-width: 1200px;*/
                padding: 40px 10%;
                margin: auto;
                display: flex;
                justify-content: space-between;
                flex-direction: row;

                .left-container {
                    width: 100%;
                    max-width: 450px;
                    margin-top: 130px;
                    position: relative;

                    .header {
                        display: none;

                        &.show {
                            display: block;

                            &.main {
                                margin-bottom: 32px;

                                .title {
                                    margin: 20px 0;
                                    font-style: normal;
                                    font-weight: normal;
                                    font-size: 48px;
                                    line-height: 60px;
                                    color: #343C38;
                                }
                            }
                        }

                        .title {
                            font-style: normal;
                            font-weight: normal;
                            font-size: 24px;
                            line-height: 30px;
                            color: #343C38;
                        }

                        .subtitle {
                            margin-bottom: 30px;
                            margin-top: 10px;
                            font-style: normal;
                            font-weight: normal;
                            font-size: 18px;
                            line-height: 45px;
                            color: #343C38;
                        }
                    }

                    .icons-list {
                        display: flex;
                        flex-direction: row;
                        align-items: flex-end;

                        .points {
                            display: flex;
                            flex-direction: row;
                            align-items: center;

                            .dot {

                                &.current {
                                    .ww-icon {
                                        div {
                                            color: #67836F !important;
                                        }
                                    }
                                }

                                .ww-icon {
                                    div {
                                        margin: 0 5px;
                                        color: rgba(52, 60, 56, 0.32) !important;
                                    }
                                }
                            }
                        }

                        .icon {
                            max-width: 40px;
                        }

                        .ww-icon {

                            div {
                                margin: 0 8px;
                            }
                        }
                    }
                }

                .right-container {
                    width: 100%;
                    max-width: 700px;
                    margin-left: 50px;
                    height: 500px;
                    position: relative;

                    .main-image {
                        border-radius: 0;

                        .image {
                            border-radius: 0;
                        }
                    }

                    .image {
                        position: absolute;
                        top: 0;
                        right: 0;
                        left: 0;

                        /*
                        NOW I SEND THE POSITION IN WWOBJECT PARAMETERS BUT YOU CAN USE THIS METHOD
                           &.image-0 img.image {
                                transform: translate(-62.2%, -45.3%) !important;
                            }

                            &.image-1 img.image {
                                transform: translate(-40.4%, -49%) !important;
                            }

                            &.image-2 img.image {
                              transform: translate(-45%, -51%) !important;

                            }
                         */

                        .format {
                            background: transparent;
                        }

                    }
                }
            }
        }

    }

    @media only screen and (max-width: 1100px) {
        .section-hello-world {
            &::v-deep {

                .content-container {

                    .left-container {
                        margin-top: 60px
                    }
                    .right-container {
                        height: 250px;
                    }
                }
            }
        }
    }

    @media only screen and (max-width: 850px) {
        .section-hello-world {
            &::v-deep {

                .content-container {
                    padding: 40px 20px;

                    .left-container {
                        margin-top: 60px
                    }
                    .right-container {
                        height: 180px;
                    }
                }
            }
        }
    }

    @media only screen and (max-width: 768px) {
        .section-hello-world {
            &::v-deep {

                .content-container {
                    padding: 30px 20px;

                    flex-direction: column-reverse;

                    .left-container {
                        position: unset;
                        margin-top: 50px;

                        .header {
                            display: none;

                            &.show {
                                display: block;

                                &.main {
                                    margin-bottom: 32px;

                                    .title {
                                        margin: 20px 0;
                                        font-size: 32px;
                                        line-height: 40px;
                                    }
                                }
                            }

                            .title {
                                margin: 20px 0;
                                font-size: 20px;
                                line-height: 30px;
                                color: #343C38;
                            }

                            .subtitle {
                                margin-bottom: 20px;
                                margin-top: 0;
                                font-size: 16px;
                                line-height: 25px;
                                color: #343C38;
                            }
                        }

                    }

                    .right-container {
                        margin: 0;
                        height: 500px;
                    }
                }
            }
        }
    }

    @media only screen and (max-width: 640px) {
        .section-hello-world {
            &::v-deep {

                .content-container {

                    .left-container {
                        margin-top: 20px
                    }

                    .right-container {
                        height: 340px;
                    }
                }
            }
        }
    }

    @media only screen and (max-width: 500px) {
        .section-hello-world {
            &::v-deep {

                .content-container {

                    .left-container {
                        margin-top: 20px
                    }

                    .right-container {
                        height: 250px;
                    }
                }
            }
        }
    }

</style>
