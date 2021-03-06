<template>
  <div class="max-w-4xl mx-auto px-2">
    <div class="flex flex-col md:flex-row">
      <div class="w-full py-4 md:px-4">
        <div
          class="bg-white border shadow-md rounded lg:px-8 pt-6 pb-8 mt-5 mb-10"
        >
          <p class="font-bold text-gray-800 text-lg text-center">
            プランのご購入ありがとうございます。<br />
            購入時に入力頂いたメールアドレスにInvoiceが送信されます。<br />
            ご確認ください。
          </p>
          <img
            src="~/assets/img/undraw_stripe_payments.png"
            alt=""
            class="w-64 mx-auto my-6"
          />
          <div>
            <nuxt-link
              to="/"
              class="block w-full lg:w-1/2 mx-auto bg-yellow-500 text-xl text-white text-center p-3 rounded-full mb-4 hover:opacity-75"
            >
              トップページへ
            </nuxt-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { ThisTypedComponentOptionsWithRecordProps } from 'vue/types/options'
import { Context } from '@nuxt/types'
import { Breadcrumb } from '../../../../core/entities/Breadcrumb'
import { Product } from '../../../../core/entities/Product'

interface DataType {
  product: Product
}

interface MethodType {}
interface ComputedType {}
interface PropType {}

export default Vue.extend({
  validate(context: Context): boolean {
    const slug = context.params.slug
    const products = context.store.getters['product/products'] || []
    return products.find((p: Product) => p.uuid === slug)
  },
  asyncData(context: Context): DataType {
    let data = null
    if (context.payload) {
      data = context.payload as { product: Product }
    } else {
      const slug = context.params.slug
      const products = context.store.getters['product/products'] || []
      const product = products.find((p: Product) => p.uuid === slug)
      data = { product }
    }
    context.store.dispatch('setPageInfo', {
      title: data.product.title,
      description: data.product.description,
      breadcrumbs: [
        {
          to: '/',
          icon: ['fas', 'laptop-code'],
          color: 'text-gray-100',
        } as Breadcrumb,
        {
          to: `/user/${data.product.author._id}`,
          name: data.product.author.name,
          color: 'text-gray-100',
        } as Breadcrumb,
        {
          to: `/plan/${data.product.uuid}`,
          name: data.product.title,
          color: 'text-gray-100',
        } as Breadcrumb,
        { name: `購入完了`, color: 'text-gray-100' } as Breadcrumb,
      ],
    })

    return {
      ...data,
    }
  },
  data(): DataType {
    return {
      product: {} as Product,
    }
  },
  head() {
    return {
      htmlAttrs: {
        lang: 'ja',
      },
      title: `${this.product.title}の購入が完了しました`,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: `購入完了 - ${this.product.description}`,
        },
        {
          hid: 'robots',
          name: 'robots',
          content: 'noindex',
        },
      ],
    }
  },
} as ThisTypedComponentOptionsWithRecordProps<Vue, DataType, MethodType, ComputedType, PropType>)
</script>
