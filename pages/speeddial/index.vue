<template>
    <DocComponent title="Vue Speed Dial Component" header="SpeedDial" description="When pressed, a floating action button can display multiple primary actions that can be performed on a page." :componentDocs="docs" :apiDocs="['SpeedDial']" />
</template>

<script>
import AccessibilityDoc from '@/doc/speeddial/AccessibilityDoc.vue';
import CircleDoc from '@/doc/speeddial/CircleDoc.vue';
import CustomDoc from '@/doc/speeddial/CustomDoc.vue';
import ImportDoc from '@/doc/speeddial/ImportDoc.vue';
import LinearDoc from '@/doc/speeddial/LinearDoc.vue';
import MaskDoc from '@/doc/speeddial/MaskDoc.vue';
import QuarterCircleDoc from '@/doc/speeddial/QuarterCircleDoc.vue';
import SemiCircleDoc from '@/doc/speeddial/SemiCircleDoc.vue';
import TooltipDoc from '@/doc/speeddial/TooltipDoc.vue';

export default {
    data() {
        return {
            docs: [
                { id: 'import', label: 'Import', component: ImportDoc },
                { id: 'linear', label: 'Linear', component: LinearDoc },
                { id: 'circle', label: 'Circle', component: CircleDoc },
                { id: 'semi-circle', label: 'Semi Circle', component: SemiCircleDoc },
                { id: 'quarter-circle', label: 'Quarter Circle', component: QuarterCircleDoc },
                { id: 'tooltip', label: 'Tooltip', component: TooltipDoc },
                { id: 'mask', label: 'Mask', component: MaskDoc },
                { id: 'custom', label: 'Custom', component: CustomDoc },
                { id: 'accessibility', label: 'Accessibility', component: AccessibilityDoc }
            ]
        };
    }
};
</script>
