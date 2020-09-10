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

            <!-- wwManager:start -->
            <div class="edit-custom-block">
                <wwObject class="icon" v-bind:ww-object="section.data.removeIcon" v-on:click="removeSlide"></wwObject>
                <wwObject class="icon" v-bind:ww-object="section.data.addIcon" v-on:click="addSlide"></wwObject>
            </div>
            <!-- wwManager:end -->
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
                zIndex: 1,
                timer: null
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
            this.startTimer();
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
                                size: 34,
                                borderWidth: 0

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
                                size: 32
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
                                size: 32,
                                paddingLeft: 20
                            }
                        }
                    });
                    needUpdate = true;
                }

                if (!this.section.data.addIcon) {
                    this.section.data.addIcon = wwLib.wwObject.getDefault({
                        type: 'ww-icon', data: {
                            icon: 'fas fa-plus',
                            style: {
                                size: 32,
                                paddingLeft: 20,
                                borderWidth: 0,
                                color: '#ef811a'
                            }
                        }
                    });
                    needUpdate = true;
                }
                if (!this.section.data.removeIcon) {
                    this.section.data.removeIcon = wwLib.wwObject.getDefault({
                        type: 'ww-icon', data: {
                            icon: 'far fa-trash-alt',
                            style: {
                                size: 32,
                                paddingLeft: 20,
                                borderWidth: 0,
                                color: '#ef811a'
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
                                    url: 'https://weweb.twic.pics/designs/436/sections/Group_12.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.87,
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
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_3.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.7,
                                    position: { x: 0, y: 0 },
                                    style: {
                                        borderRadius: 100,
                                        ratio: 1
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
                                        en: '42CAP had a conviction in the Gig Economy, which intrigued us.'
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
                        },
                        {
                            image: wwLib.wwObject.getDefault({
                                type: 'ww-image',
                                data: {
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_5.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.63,
                                    position: { x: -7.1, y: 11.4 },
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
                                        en: 'The round was over subscribed, but I wanted to have 42CAP due to their entrepreneurial experience.'
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
                                    zoom: 0.63,
                                    position: { x: -19.1, y: -0.9 },
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
                                        en: 'The round was over subscribed, but I wanted to have 42CAP due to their entrepreneurial experience.'
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
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_5.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.6,
                                    position: { x: -14, y: -14.4 },
                                    style: {
                                        borderRadius: 100,
                                        ratio: 1
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
                                        en: '42CAP had a conviction in the Gig Economy, which intrigued us.'
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
                        },
                        {
                            image: wwLib.wwObject.getDefault({
                                type: 'ww-image',
                                data: {
                                    url: 'https://weweb.twic.pics/designs/436/sections/Ellipse_3.png?%27}twic=v1/quality=85/resize=1024',
                                    zoom: 0.63,
                                    position: { x: 12.8, y: -11.5 },
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
                                        en: 'The round was over subscribed, but I wanted to have 42CAP due to their entrepreneurial experience.'
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
                        }
                    ];
                    needUpdate = true;
                }

                if (needUpdate) {
                    this.sectionCtrl.update(this.section);
                }
            },
            startTimer() {
                this.timer = setInterval(() => {
                    this.showSlides(this.slideIndex += 1);
                }, 4000);
                /* wwManager:start */
                clearInterval(this.timer);
                /* wwManager:end */
            },
            nextSlide(index) {
                if (this.timer) {
                    clearInterval(this.timer);
                }
                this.showSlides(this.slideIndex += index);
                setTimeout(this.startTimer(), 1500);
            },
            currentSlide(n) {
                if (this.timer) {
                    clearInterval(this.timer);
                }
                this.showSlides(this.slideIndex = n);
                setTimeout(this.startTimer(), 1500);
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
                    this.section.data.contentList[i].mainImage = false;
                }

                slides[this.slideIndex - 1].classList.add('active');
                slides[this.slideIndex - 1].style.zIndex = (this.zIndex++).toString();
                dots[this.slideIndex - 1].classList.add('current');
                headers[this.slideIndex - 1].classList.add('show');
            },

            /* wwManager:start */
            add(list, options) {
                list.splice(options.index, 0, options.wwObject);

                this.sectionCtrl.update(this.section);
            },
            remove(list, options) {
                list.splice(options.index, 1);

                this.sectionCtrl.update(this.section);
            },
            getNewSlide() {
                const position = {
                    left: { x: 7.7, y: 1.5 },
                    right: { x: -12.2, y: 4.7 }
                };

                const zoom = {
                    right: 0.556,
                    left: 0.6
                };
                const objectData = {
                    slide5: {
                        zoom: 0.6,
                        position: { x: 7.7, y: 1.5 }
                    }
                };
                if ((this.section.data.contentList.length - 2) % 2 !== 0) {
                    objectData.position = position.left;
                    objectData.zoom = zoom.left;
                } else {
                    objectData.position = position.right;
                    objectData.zoom = zoom.right;
                }

                const defaultObj = {
                    image: wwLib.wwObject.getDefault({
                        type: 'ww-image',
                        data: {
                            url: 'https://weweb.twic.pics/designs/436/sections/default.jpg?%27}twic=v1/quality=85/resize=1024',
                            zoom: objectData.zoom,
                            position: objectData.position,
                            style: {
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
                                en: 'Default text'
                            }
                        }
                    }),
                    subtitle: wwLib.wwObject.getDefault({
                        type: 'ww-text',
                        data: {
                            text: {
                                en: 'Default text'
                            }
                        }
                    }),
                    isActive: false
                };


                const card = JSON.parse(JSON.stringify(defaultObj));
                wwLib.wwUtils.changeUniqueIds(card);
                return card;
            },
            addSlide() {
                const newCard = this.getNewSlide();
                this.section.data.contentList.push(newCard);
                this.sectionCtrl.update(this.section);
            },
            removeSlide(index) {
                if (!this.section.data.contentList.length) {
                    return;
                }
                if (!index) {
                    index = this.slideIndex - 1;
                }

                this.section.data.contentList.splice(index, 1);

                this.sectionCtrl.update(this.section);
            }
            /* wwManager:end */
        }
    };
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang="scss" scoped>
    .section-hello-world {
        position: relative;
        height: calc(100vh - 100px);

        &::v-deep {

            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }

            .content-container {
                width: 100%;
                position: relative;
                /*max-width: 1200px;*/
                padding: 40px 10%;
                margin: auto;
                display: flex;
                justify-content: space-between;
                flex-direction: row;
                align-items: center;

                .left-container {
                    width: 100%;
                    max-width: 450px;
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
                    height: 600px;
                    position: relative;

                    .image {
                        position: absolute;
                        right: 0;
                        left: 0;
                        top: 50%;
                        transform: translateY(-50%);

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

                            img {
                                border-radius: 50%;
                            }
                        }

                    }

                    .main-image {
                        border-radius: 0;

                        .format {
                            img {
                                border-radius: 0;
                            }
                        }

                    }
                }

                .edit-custom-block {
                    display: flex;
                    position: absolute;
                    top: 15px;
                    right: 20px;
                    cursor: pointer;

                    .icon {
                        margin-left: 10px;
                    }
                }
            }

            .icons {
                position: absolute;
                bottom: 0;
                left: 50%;
                transform: translateX(-50%);
            }
        }

    }

    @media only screen and (max-width: 1100px) {
        .section-hello-world {
            height: auto;

            &::v-deep {

                .content-container {

                    .left-container {
                        margin-top: 60px
                    }

                    .right-container {
                        height: 250px;
                    }
                }

                .icons {
                    position: relative;
                }
            }
        }
    }

    @media only screen and (max-width: 850px) {
        .section-hello-world {
            &::v-deep {

                .content-container {
                    padding: 40px 20px;


                    .right-container {
                        height: 280px;
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
                        height: 450px;
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
                        height: 300px;
                    }
                }
            }
        }
    }

    @media only screen and (max-width: 400px) {
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
