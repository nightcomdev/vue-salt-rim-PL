<template>
    <div class="ingredient__image" :style="{ 'background-color': backgroundColor }">
        <img :src="mainIngredientImageUrl" :alt="ingredient.name">
    </div>
</template>
<script>
import { thumbHashToDataURL } from 'thumbhash'
import ApiRequests from './../../ApiRequests.js'

export default {
    props: {
        ingredient: {
            type: Object,
            default() {
                return {}
            }
        }
    },
    computed: {
        placeholderImage() {
            if (this.ingredient.images.length > 0) {
                return thumbHashToDataURL(
                    Uint8Array.from(atob(this.ingredient.images[0].placeholder_hash), c => c.charCodeAt(0))
                )
            }

            return ''
        },
        backgroundColor() {
            const hex = this.ingredient.color || '#51274c'

            let c
            if (/^#([A-Fa-f0-9]{3}){1,2}$/.test(hex)) {
                c = hex.substring(1).split('')
                if (c.length == 3) {
                    c = [c[0], c[0], c[1], c[1], c[2], c[2]]
                }
                c = '0x' + c.join('')
                return 'rgba(' + [(c >> 16) & 255, (c >> 8) & 255, c & 255].join(',') + ',.13)'
            }

            return hex
        },
        mainIngredientImageUrl() {
            if (this.ingredient.image_url) {
                return this.ingredient.image_url
            }

            if (this.ingredient.images && this.ingredient.images.length > 0) {
                return ApiRequests.imageThumbUrl(this.ingredient.images[0].id)
            }

            return '/no-ingredient.png'
        }
    }
}
</script>
<style scoped>
.ingredient__image {
    width: 70px;
    height: 70px;
    border-radius: var(--radius-1);
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    overflow: hidden;
    background-color: #fff;
}

.ingredient__image img {
    height: 100px;
    position: absolute;
    top: 10px;
    transition: top ease-in-out .2s;
}

.ingredient__image.ingredient__image--small {
    width: 45px;
    height: 45px;
}

.ingredient__image.ingredient__image--small img {
    height: 70px;
}

.ingredient__image:hover img {
    top: -15px;
}
</style>
