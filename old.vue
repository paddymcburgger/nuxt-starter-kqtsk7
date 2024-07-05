<template>
  <div class="invoice-component">
    <!-- Invoice Input Section -->
    <div class="invoice-input">
      <h2>Enter Invoice Details</h2>

      <!-- Client Information -->

      <!-- Seller Information -->
      <h3>Seller Information</h3>
      <div class="form-group">
        <label>Company Name:</label>
        <input
          class="styled-input"
          v-model="companyNameInput"
          type="text"
          placeholder="Company Name"
        />
      </div>
      <div class="form-group">
        <label>Company Address:</label>
        <input
          class="styled-input"
          v-model="companyAddressInput"
          type="text"
          placeholder="Company Address"
        />
      </div>
      <div class="form-group">
        <label>Company Email:</label>
        <input
          class="styled-input"
          v-model="companyEmailInput"
          type="text"
          placeholder="Company Email"
        />
      </div>
      <div class="form-group">
        <label>Company Number:</label>
        <input
          class="styled-input"
          v-model="companyNumberInput"
          type="text"
          placeholder="Company Number"
        />
      </div>

      <!-- Invoice Information -->
      <h3>Invoice Information</h3>
      <div class="form-group">
        <label>Invoice Number:</label>
        <input
          class="styled-input"
          v-model="invoiceNumberInput"
          type="text"
          placeholder="Invoice Number"
        />
      </div>

      <!-- Invoice Date Input Field -->
      <div class="form-group">
        <input
          v-if="!invoiceDateFocused"
          class="styled-input"
          v-model="invoiceDatePlaceholder"
          placeholder="Invoice Date"
          @focus="focusInvoiceDate"
        />
        <input
          v-else
          class="styled-input"
          v-model="invoiceDateInput"
          type="date"
          @blur="blurInvoiceDate"
        />
      </div>

      <!-- Due Date Input Field -->
      <div class="form-group">
        <input
          v-if="!dueDateFocused"
          class="styled-input"
          v-model="dueDatePlaceholder"
          placeholder="Due Date"
          @focus="focusDueDate"
        />
        <input
          v-else
          class="styled-input"
          v-model="dueDateInput"
          type="date"
          @blur="blurDueDate"
        />
      </div>

      <!-- Buyer Information -->
      <h3>Buyer Information</h3>
      <div class="form-group">
        <label>Client Name:</label>
        <input
          class="styled-input"
          v-model="clientNameInput"
          type="text"
          placeholder="Client Name"
        />
      </div>
      <div class="form-group">
        <label>Client Address:</label>
        <input
          class="styled-input"
          v-model="clientAddressInput"
          type="text"
          placeholder="Client Address"
        />
      </div>
      <div class="form-group">
        <label>Client Email:</label>
        <input
          class="styled-input"
          v-model="clientEmailInput"
          type="email"
          placeholder="Client Email"
        />
      </div>
      <div class="form-group">
        <label>Client Number:</label>
        <input
          class="styled-input"
          v-model="clientNumberInput"
          type="text"
          placeholder="Client Number"
        />
      </div>

      <!-- Service/Item Group -->
      <h3>Service/Item Group</h3>
      <div
        class="form-group"
        v-for="(inputRow, index) in inputRows"
        :key="index"
      >
        <input
          class="styled-input"
          v-model="inputRow.item"
          type="text"
          placeholder="Item/Service"
        />
        <input
          class="styled-input"
          v-model="inputRow.price"
          type="text"
          placeholder="Price"
        />
        <input
          class="styled-input"
          v-model="inputRow.quantity"
          type="text"
          placeholder="Quantity"
        />
        <button v-if="index > 0" @click="removeItem(index)">Remove</button>
      </div>
      <div class="form-group">
        <input
          class="styled-input"
          v-model="vatPercentage"
          type="number"
          :placeholder="vatPlaceholder"
        />
        <input
          class="styled-input"
          v-model="discountPercentage"
          type="number"
          :placeholder="discountPlaceholder"
        />
      </div>
      <button class="add-button" @click="addItem">
        <span>+</span> Add Item
      </button>
    </div>

    <!-- Invoice Output Section -->
    <div class="invoice-output" ref="invoicePreview">
      <div class="invoice-header">
        <!-- Invoice Information -->
        <div class="invoice-info">
          <h3>Invoice Information</h3>
          <div>
            <label>Invoice Number:</label>
            <span>{{ invoiceNumberInput }}</span>
          </div>
          <div>
            <label>Invoice Date:</label>
            <span>{{ invoiceDateInput }}</span>
          </div>
          <div>
            <label>Due Date:</label>
            <span>{{ dueDateInput }}</span>
          </div>
        </div>

        <!-- Client Information -->
        <div class="client-info">
          <h3>Client Information</h3>
          <div>
          <span>{{ clientNameInput || 'Enter Client Name' }}</span>
        </div>
        <div>
          <span>{{ clientAddressInput || 'Enter Client Address' }}</span>
        </div>
        <div>
          <span>{{ clientEmailInput || 'Enter Client Email' }}</span>
        </div>
        <div>
          <span>{{ clientNumberInput || 'Enter Client Number' }}</span>
        </div>
        </div>
        
      </div>


 <div class="invioce-content">
      <!-- Invoice Table -->
      <div class="invoice-table">
        <h3>Invoice Table</h3>
        <table>
          <thead>
            <tr>
              <th>Item/Service</th>
              <th>Price</th>
              <th v-if="showQuantityColumn && showQuantityInTable">Quantity</th>
              <th v-if="showQuantityColumn && showQuantityInTable">Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in items" :key="index">
              <td>{{ item.item }}</td>
              <td>{{ item.price }}</td>
              <td v-if="showQuantityColumn && showQuantityInTable">
                {{ item.quantity }}
              </td>
              <td v-if="showQuantityColumn && showQuantityInTable">
                {{ item.total }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Invoice Summary -->
      <div class="invoice-summary">
        <h3>Invoice Summary</h3>
        <table>
          <thead>
            <tr>
              <th>Total Sum</th>
              <th>VAT ({{ vatPercentage }}%)</th>
              <th>Discount ({{ discountPercentage }}%)</th>
              <th>Total Due</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>{{ totalSum }}</td>
              <td>{{ vatAmount }}</td>
              <td>{{ discountAmount }}</td>
              <td>{{ totalDue }}</td>
            </tr>
          </tbody>
        </table>
      </div>
  </div>

      <!-- Seller Information (Footer) -->
      <div class="seller-info">
        <h3>Seller Information</h3>
        <div>
        <span>{{ companyNameInput || 'Enter Company Name' }}</span>
      </div>
      <div>
        <span>{{ companyAddressInput || 'Enter Company Address' }}</span>
      </div>
      <div>
        <span>{{ companyEmailInput || 'Enter Company Email' }}</span>
      </div>
      <div>
        <span>{{ companyNumberInput || 'Enter Company Number' }}</span>
      </div>
    </div>
    </div>

    <div>
    <!-- Button to Print -->
    <button @click="printInvoice">Print/Export to PDF</button>
  </div>
  </div>
</template>

<script>




export default {
  data() {
    return {
      inputRows: [{ item: '', price: '', quantity: '' }],
      items: [],
      invoiceNumberInput: '',
      invoiceDateInput: '',
      dueDateInput: '',
      invoiceDatePlaceholder: '',
      dueDatePlaceholder: '',
      invoiceDateFocused: false,
      dueDateFocused: false,
      clientNameInput: '',
      clientAddressInput: '',
      clientEmailInput: '',
      clientNumberInput: '',
      companyNameInput: '',
      companyAddressInput: '',
      companyEmailInput: '',
      companyNumberInput: '',
      vatPercentage: '',
      discountPercentage: '',
    };
  },
  computed: {
    vatPlaceholder() {
      return this.vatPercentage === '' ? 'VAT Percentage' : '';
    },
    discountPlaceholder() {
      return this.discountPercentage === '' ? 'Discount Percentage' : '';
    },
    showQuantityColumn() {
      return this.inputRows.some(
        (row) => row.quantity !== '' && parseFloat(row.quantity) >= 0
      );
    },
    showQuantityInTable() {
      return this.inputRows.some(
        (row) => row.quantity !== '' && parseFloat(row.quantity) > 0
      );
    },
    totalSum() {
      return this.items.reduce(
        (total, item) => total + parseFloat(item.total || 0),
        0
      );
    },
    vatAmount() {
      return (
        (this.totalSum * parseFloat(this.vatPercentage || 0)) /
        100
      ).toFixed(2);
    },
    discountAmount() {
      return (
        (this.totalSum * parseFloat(this.discountPercentage || 0)) /
        100
      ).toFixed(2);
    },
    totalDue() {
      return (
        this.totalSum +
        parseFloat(this.vatAmount) -
        parseFloat(this.discountAmount)
      ).toFixed(2);
    },
  },

  methods: {
    addItem() {
      this.inputRows.push({ item: '', price: '', quantity: '' });
    },
    removeItem(index) {
      if (index > 0) {
        this.inputRows.splice(index, 1);
      }
    },
    focusInvoiceDate() {
      this.invoiceDateFocused = true;
      this.$nextTick(() => {
        this.$refs.invoiceDateInput.focus();
      });
    },
    blurInvoiceDate() {
      if (!this.invoiceDateInput) {
        this.invoiceDateFocused = false;
      }
    },
    focusDueDate() {
      this.dueDateFocused = true;
      this.$nextTick(() => {
        this.$refs.dueDateInput.focus();
      });
    },
    blurDueDate() {
      if (!this.dueDateInput) {
        this.dueDateFocused = false;
      }
    },
    printInvoice() {
      const printSection = this.$refs.invoicePreview;
      const printWindow = window.open('', '', 'height=800,width=600');
      printWindow.document.write('<html><head><title>Invoice</title>');
      const styles = document.querySelectorAll('style, link[rel="stylesheet"]');
      styles.forEach(style => {
        printWindow.document.write(style.outerHTML);
      });
      printWindow.document.write('</head><body>');
      printWindow.document.write(printSection.outerHTML);
      printWindow.document.write('</body></html>');
      printWindow.document.close();
      printWindow.print();
    }
  },
  watch: {
    inputRows: {
      handler(newValue) {
        this.items = newValue.map((row) => ({
          item: row.item,
          price: row.price,
          quantity: parseFloat(row.quantity || 0),
          total: row.price * (parseFloat(row.quantity || 0) || 1),
        }));
      },
      deep: true,
    },
  },
};
</script>




<style scoped>

.invioce-content {
  min-height: 100%;
}

.invoice-component div.invoice-info > label {
  display: none;
}

.invoice-component div.form-group > label {
  display: none;
}

.invoice-component {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: flex-end;
  gap: 20px;
  min-height: 100vh;
  font-family: Arial, sans-serif;
  margin: 0 auto;
  max-width: 100%;
  padding: 20px;
  background-color: #333;
  color: #f9f9f9;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.invoice-input,
.invoice-output {
  width: 100%;
  max-width: 210mm; /* A4 width */
  padding-top: 20px;
  margin-top: 20px;
}

.invoice-output {
  background-color: #fff;
  color: #000;
  max-height: 614px;
  padding: 20mm;
  box-sizing: border-box;
  aspect-ratio: 210 / 297; /* Maintain A4 aspect ratio */
}

@media (max-width: 1200px) {
  .invoice-input,
  .invoice-output {
    width: 100%;
    max-width: unset;
    aspect-ratio: unset;
  }
}

.invoice-output h2 {
  color: #333;
  font-size: 18px;
  margin-bottom: 20px;
}

.print-button button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.print-button button:hover {
  background-color: #0056b3;
}

.add-button {
  background-color: #fb8700;
  height: 38px;
  width: 150px;
  border: none;
  border-radius: 50px;
  padding-left: 12px;
  padding-right: 12px;
  padding-top: 7px;
  padding-bottom: 7px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  color: #ffffff;
  font-size: 16px;
  font-family: 'Inter', sans-serif;
}

.add-button span {
  font-size: 20px;
}

.styled-input {
  width: calc(100% - 18px);
  padding: 8px;
  border: none;
  border-bottom: 1px solid #ccc;
  margin-bottom: 10px;
  box-sizing: border-box;
  background-color: #444;
  color: #fff;
  border-bottom-color: #555;
}

.styled-input:focus {
  outline: none;
  border-bottom: 1px solid #333;
  border-bottom-color: #f9f9f9;
}

.styled-input::placeholder {
  font-weight: thin;
  color: #bbb;
}

.invoice-output h3 {
  color: #333;
  font-size: 18px;
  margin-bottom: 10px;
}

.invoice-output label,
.invoice-output span {
  display: in;
  margin-bottom: 5px;
  color: #555;
}

.invoice-output table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 15px;
}

.invoice-output th,
.invoice-output td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

.invoice-output th {
  background-color: #555;
  color: #f9f9f9;
}

.invoice-output td {
  background-color: #333;
  color: #f9f9f9;
}

.invoice-output .invoice-summary table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
}

.invoice-output .invoice-summary th {
  background-color: #f2f2f2;
  color: #333;
  border: 1px solid #ccc;
  padding: 8px;
}

.invoice-output .invoice-summary td {
  border: 1px solid #ccc;
  padding: 8px;
}

.invoice-output .print-button button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.invoice-output .print-button button:hover {
  background-color: #0056b3;
}

.invoice-header {
  display: flex;
  justify-content: space-between;
}

.invoice-info,
.client-info {
  width: 45%;
}

.client-info {
  text-align: right;
}

.invoice-table,
.invoice-summary,
.seller-info {
  margin-top: 20px;
}

@media print {
  @page {
    size: A4 portrait;
    margin: 0;
  }
  body {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  .invoice-output {
    width: 100%;
    height: auto;
    margin: 0;
    padding: 20mm;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    page-break-inside: avoid; /* Prevent page break inside element */
  }
}

@media screen and (min-width: 1200px) {
  .invoice-component {
    flex-direction: row;
  }
  .invoice-output {
    order: 1;
    max-width: 210mm;
    flex: 0 0 45%; /* Adjust based on your layout */
  }
  .invoice-input {
    order: 1;
    flex: 1;
  }
}

@media screen and (max-width: 1199px) {
  .invoice-component {
    flex-direction: column;
  }
  .invoice-output {
    order: 2;
    width: 100%;
  }
}
</style>

