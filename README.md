# ERPNext Project Manager- Kustomisasi Modul Sales Order

Dokumen ini menjelaskan hasil kustomisasi modul Sales Order dalam sistem ERPNext


## Tujuan Customized Sales Order dengan beberapa ketentuan

1. Menambahkan *field "Delivery Mode"* (Delivery / Pickup) di Sales Order.
2. Menambahkan *validasi* agar "Shipping Address" wajib diisi jika Delivery Mode adalah "Delivery".
3. Membuat tombol *"Create Delivery Note"* langsung dari form Sales Order.
4. Menampilkan *popup alert Total Amount* setiap kali Sales Order disimpan.
5. Mengatur *item table* agar menampilkan hingga *13 kolom* (opsional).


### 1. *Penambahan Custom Field: Delivery Mode*

- *Doctype*: Sales Order
- *Field Type*: Select
- *Label*: Delivery Mode
- *Fieldname*: delivery_mode
- *Options*: Delivery/Pickup
Ditambahkan melalui *Customize Form*.

===============================================================================================================
### 2. *Validasi Shipping Address*

*File*: Client Script → Sales Order - Delivery Mode Validation

js Syntax:
frappe.ui.form.on('Sales Order', {
  validate: function(frm) {
      if (frm.doc.custom_delivery_mode === 'Delivery' && !frm.doc.shipping_address) {
          frappe.throw(('Shipping Address wajib diisi jika Delivery Mode adalah "Delivery".'));
      }
  }
});
===============================================================================================================

### 3. *Tombol Create Delivery Note Button*

js Syntax:
*File*: Client Script → Sales Order - Delivery Mode Validation
frappe.ui.form.on('Sales Order', {
    refresh: function(frm) {
        if (!frm.is_new() && frm.doc.docstatus === 1) {
            frm.add_custom_button(('Create Delivery Note'), function() {
                frappe.model.open_mapped_doc({
                    method: "erpnext.selling.doctype.sales_order.sales_order.make_delivery_note",
                    frm: frm
                });
            }, __('Create'));
        }
    }
});
===============================================================================================================

### 4. *Tombol Create Popup Alert Amount Saved*

js Syntax:
frappe.ui.form.on('Sales Order', {
    after_save: function(frm) {
        frappe.msgprint({
            title: __('Total Order'),
            message: __('Grand Total: {0} {1}', [frm.doc.grand_total, frm.doc.currency]),
            indicator: 'blue'
        });
    }
});
===============================================================================================================

### 5. Menampilkan 13 Kolom di Item Table

Dilakukan melalui menu *Customize Form*:

- *Doctype*: Sales Order
- *Field*: items (child table)
- Checklist kolom yang ingin ditampilkan di bagian Grid: misalnya item_code, item_name, qty, uom, rate, amount, dll.
- Total kolom yang ditandai *Print/Report/Visible in Grid View*: 13 kolom.

> Tidak diperlukan script tambahan karena sudah ditangani via konfigurasi UI.
