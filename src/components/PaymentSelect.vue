<template>
    <div class="col">
        <!-- <input type="text" class="form-control" placeholder="Payment Method"> -->
        <div class="dropdown">
            <button class="btn btn-secondary dropdown-toggle" type="button" id="dropdownMenuButton" data-bs-toggle="dropdown" aria-expanded="false">
                {{ selectedPaymentText || 'Payment Method' }}
            </button>
            <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                <li><a class="dropdown-item" href="#" @click.prevent="selectedPayment('Cash on Delivery')">Cash on Delivery</a></li>
                <li><a class="dropdown-item" href="#"  @click.prevent="selectedPayment('Hand Payment')">Hand Payment</a></li>
                <li><a class="dropdown-item" href="#"  @click.prevent="selectedPayment('Mobile Banking')">Mobile Banking</a></li>
            </ul>
        </div>
    </div>
</template>
<script>
export default {
    name: 'PaymentSelect',
    data() {
        return {
            selectedPaymentText: ''
        }
    },
    methods: {
        selectedPayment(payment) {
            this.selectedPaymentText = payment;
            this.$emit('payment-selected', payment);
        }
    }
}
</script>