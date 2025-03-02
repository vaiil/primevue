<template>
    <DocSectionText v-bind="$attrs">
        <p>
            Dialogs can be created dynamically with any component as the content using a DialogService along with a <i>DynamicDialog</i> component. Ideal location of a DynamicDialog is the main template so that it can be used by any component within
            the application.
        </p>
    </DocSectionText>
    <div class="card flex justify-content-center">
        <Button label="Select a Product" icon="pi pi-search" @click="showProducts" />

        <DynamicDialog />
    </div>
    <DocSectionCode :code="code" :extFiles="extFiles" :service="['ProductService']" />
</template>

<script>
import Button from 'primevue/button';
import { h } from 'vue';
import ProductListDemo from './demo/ProductListDemo';

export default {
    data() {
        return {
            code: {
                basic: `
<Button label="Select a Product" icon="pi pi-search" @click="showProducts" />

<DynamicDialog />`,
                options: `
<template>
    <div class="card flex justify-content-center">
        <Button label="Select a Product" icon="pi pi-search" @click="showProducts" />
        <Toast />
        <DynamicDialog />
    </div>
</template>

<script>
import Button from 'primevue/button';
import { h, defineAsyncComponent } from 'vue';
const ProductListDemo = defineAsyncComponent(() => import('./components/ProductListDemo.vue'));

export default {
    methods: {
        showProducts() {
            const dialogRef = this.$dialog.open(ProductListDemo, {
                props: {
                    header: 'Product List',
                    style: {
                        width: '50vw'
                    },
                    breakpoints: {
                        '960px': '75vw',
                        '640px': '90vw'
                    },
                    modal: true
                },
                templates: {
                    footer: () => {
                        return [
                            h(Button, { label: 'No', icon: 'pi pi-times', onClick: () => dialogRef.close({ buttonType: 'No' }), class: 'p-button-text' }),
                            h(Button, { label: 'Yes', icon: 'pi pi-check', onClick: () => dialogRef.close({ buttonType: 'Yes' }), autofocus: true })
                        ];
                    }
                },
                onClose: (options) => {
                    const data = options.data;

                    if (data) {
                        const buttonType = data.buttonType;
                        const summary_and_detail = buttonType ? { summary: 'No Product Selected', detail: \`Pressed '\${buttonType}' button\` } : { summary: 'Product Selected', detail: data.name };

                        this.$toast.add({ severity: 'info', ...summary_and_detail, life: 3000 });
                    }
                }
            });
        }
    }
}
<\/script>`,
                composition: `
<template>
    <div class="card flex justify-content-center">
        <Button label="Select a Product" icon="pi pi-search" @click="showProducts" />
        <Toast />
        <DynamicDialog />
    </div>
</template>

<script setup>
import { h, defineAsyncComponent } from 'vue';
import { useDialog } from 'primevue/usedialog';
import { useToast } from 'primevue/usetoast';
import Button from 'primevue/button';
const ProductListDemo = defineAsyncComponent(() => import('./components/ProductListDemo.vue'));

const dialog = useDialog();
const toast = useToast();

const showProducts = () => {
    const dialogRef = dialog.open(ProductListDemo, {
        props: {
            header: 'Product List',
            style: {
                width: '50vw',
            },
            breakpoints:{
                '960px': '75vw',
                '640px': '90vw'
            },
            modal: true
        },
        templates: {
            footer: () => {
                return [
                    h(Button, { label: "No", icon: "pi pi-times", onClick: () => dialogRef.close({ buttonType: 'No' }), class: "p-button-text" }),
                    h(Button, { label: "Yes", icon: "pi pi-check", onClick: () => dialogRef.close({ buttonType: 'Yes' }), autofocus: true})
                ]
            }
        },
        onClose: (options) => {
            const data = options.data;
            if (data) {
                const buttonType = data.buttonType;
                const summary_and_detail = buttonType ? { summary: 'No Product Selected', detail: \`Pressed '\${buttonType}' button\` } : { summary: 'Product Selected', detail: data.name };

                toast.add({ severity:'info', ...summary_and_detail, life: 3000 });
            }
        }
    });
}
<\/script>`,
                data: `
/* ProductService */        
{
    id: '1000',
    code: 'f230fh0g3',
    name: 'Bamboo Watch',
    description: 'Product Description',
    image: 'bamboo-watch.jpg',
    price: 65,
    category: 'Accessories',
    quantity: 24,
    inventoryStatus: 'INSTOCK',
    rating: 5
},
...`
            },
            extFiles: {
                options: {
                    'src/components/ProductListDemo.vue': {
                        content: `
<template>
	<div>
        <div class="flex justify-content-end mt-1 mb-3">
            <Button icon="pi pi-external-link" label="Nested Dialog" outlined severity="success" @click="showInfo" />
        </div>
        <DataTable :value="products">
			<Column field="code" header="Code"></Column>
			<Column field="name" header="Name"></Column>
            <Column header="Image">
                <template #body="slotProps">
                    <img :src="'https://primefaces.org/cdn/primevue/images/product/' + slotProps.data.image" :alt="slotProps.data.name" class="shadow-2 w-4rem" />
                </template>
            </Column>
			<Column field="category" header="Category"></Column>
			<Column field="quantity" header="Quantity"></Column>
            <Column style="width:5rem">
                <template #body="slotProps">
                    <Button type="button" icon="pi pi-plus" text rounded @click="selectProduct(slotProps.data)"></Button>
                </template>
            </Column>
		</DataTable>

	</div>
</template>

<script>
import { ProductService } from '@/service/ProductService';
import InfoDemo from './InfoDemo.vue';

export default {
    inject: ['dialogRef'],
    data() {
        return {
            products: null
        }
    },
    mounted() {
        ProductService.getProductsSmall().then(data => this.products = data.slice(0,5));
    },
    methods: {
        selectProduct(data) {
            this.dialogRef.close(data);
        },
        showInfo() {
            this.$dialog.open(InfoDemo, {
                props: {
                    header: 'Information',
                    modal: true,
                    dismissableMask: true
                },
                data: {
                    totalProducts: this.products ? this.products.length : 0
                }
            });
        }
    }
}
<\/script>`
                    },
                    'src/components/InfoDemo.vue': {
                        content: `
<template>
    <div>
        <p>There are <strong>{{totalProducts}}</strong> products in total in this list.</p>
        <div class="flex justify-content-end">
            <Button type="button" label="Close" @click="closeDialog"></Button>
        </div>
    </div>
</template>

<script>
export default {
    inject: ['dialogRef'],
    data() {
        return {
            totalProducts: 0
        }
    },
    mounted() {
        this.totalProducts = this.dialogRef.data.totalProducts;
    },
    methods: {
        closeDialog() {
            this.dialogRef.close();
        }
    }
}
<\/script>
                            `
                    }
                },
                composition: {
                    'src/components/ProductListDemo.vue': {
                        content: `
<template>
	<div>
        <div class="flex justify-content-end mt-1 mb-3">
            <Button icon="pi pi-external-link" label="Nested Dialog" outlined severity="success" @click="showInfo" />
        </div>
        <DataTable :value="products">
			<Column field="code" header="Code"></Column>
			<Column field="name" header="Name"></Column>
            <Column header="Image">
                <template #body="slotProps">
                    <img :src="'https://primefaces.org/cdn/primevue/images/product/' + slotProps.data.image" :alt="slotProps.data.name" class="shadow-2 w-4rem" />
                </template>
            </Column>
			<Column field="category" header="Category"></Column>
			<Column field="quantity" header="Quantity"></Column>
            <Column style="width:5rem">
                <template #body="slotProps">
                    <Button type="button" icon="pi pi-plus" text rounded @click="selectProduct(slotProps.data)"></Button>
                </template>
            </Column>
		</DataTable>

	</div>
</template>

<script setup>
import { ref, onMounted, inject } from "vue";
import { useDialog } from "primevue/usedialog";
import { ProductService } from "@/service/ProductService";
import InfoDemo from "./InfoDemo.vue";

const dialogRef = inject("dialogRef");
const dialog = useDialog();
const products = ref(null);

onMounted(() => {
    ProductService
        .getProductsSmall()
        .then((data) => (products.value = data.slice(0, 5)));
});

const selectProduct = (data) => {
    dialogRef.value.close(data);
};

const showInfo = () => {
    dialog.open(InfoDemo, {
        props: {
            header: "Information",
            modal: true,
            dismissableMask: true,
        },
        data: {
            totalProducts: products.value ? products.value.length : 0,
        }
    });
};
<\/script>`
                    },
                    'src/components/InfoDemo.vue': {
                        content: `
<template>
    <div>
        <p>There are <strong>{{totalProducts}}</strong> products in total in this list.</p>
        <div class="flex justify-content-end">
            <Button type="button" label="Close" @click="closeDialog"></Button>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, inject } from "vue";

const totalProducts = ref(0);
const dialogRef = inject("dialogRef");

onMounted(() => {
    totalProducts.value = dialogRef.value.data.totalProducts;
});

const closeDialog = () => {
    dialogRef.value.close();
};
<\/script>
                            `
                    }
                }
            }
        };
    },
    methods: {
        showProducts() {
            const dialogRef = this.$dialog.open(ProductListDemo, {
                props: {
                    header: 'Product List',
                    style: {
                        width: '50vw'
                    },
                    breakpoints: {
                        '960px': '75vw',
                        '640px': '90vw'
                    },
                    modal: true
                },
                templates: {
                    footer: () => {
                        return [
                            h(Button, { label: 'No', icon: 'pi pi-times', onClick: () => dialogRef.close({ buttonType: 'No' }), class: 'p-button-text' }),
                            h(Button, { label: 'Yes', icon: 'pi pi-check', onClick: () => dialogRef.close({ buttonType: 'Yes' }), autofocus: true })
                        ];
                    }
                },
                onClose: (options) => {
                    const data = options.data;

                    if (data) {
                        const buttonType = data.buttonType;
                        const summary_and_detail = buttonType ? { summary: 'No Product Selected', detail: `Pressed '${buttonType}' button` } : { summary: 'Product Selected', detail: data.name };

                        this.$toast.add({ severity: 'info', ...summary_and_detail, life: 3000 });
                    }
                }
            });
        }
    }
};
</script>
