# -*- encoding: utf-8 -*-

##############################################################################
import time
from osv import osv, fields
from tools.translate import _
from openerp.tools.float_utils import float_compare
from num2words import num2words
import openerp.addons.decimal_precision as dp
import pdb


# To add new fields in cheque payment voucher

class cheque_payment(osv.osv):

    _name = "cheque.payment.voucher"

    def _amount_in_words(self,cr,uid,ids,field_names=None,arg=False,context=None):
	res = {}
	for line in self.browse(cr,uid,ids):
		total = 0.0
		words = 'Zero'
		if line.cheque_line:
			for cheque in line.cheque_line:
				qty = cheque.qty
				unit_price = cheque.price_unit
				total += qty * unit_price
			amount_total = round(total)
			amount_words = num2words(amount_total) + ' only'
			amounts_words_upper = amount_words.upper()
			words = amounts_words_upper or 'Zero'
		res[line.id] = words
	return res

    def _amount_all(self, cr, uid, ids, field_name, arg, context=None):
        res = {}
        for vc in self.browse(cr, uid, ids, context=context):
            val = val1 = 0.0
            for line in vc.cheque_line:
               val1 += line.amount
        
            res[vc.id] = val1
        return res

    _columns = {
            'name':fields.char('Cheque/Mpesa/TT',size=250,required=True),
            'payee':fields.many2one('res.partner','Payee',required=True),
            'date':fields.date('Date of requisition',required=True),
            'cheque_line':fields.one2many('cheque.payment.line','cheque_id','Chequelines'),
	    'supplier_balance':fields.float('Supplier balance'),
	    'value_in_hand':fields.float('Inventory value in hand'),    
	    'cash_in_hand':fields.float('Cash in hand'),
	    'prepare':fields.many2one('res.users','Prepared by '),
	    'check':fields.many2one('res.users','Checked by '),
	    'authorise':fields.many2one('res.users','Authorised by '),
	    'receive':fields.many2one('res.users','Received by '),
	    'amount_words':fields.function(_amount_in_words, string = 'Amount in words',store =True, type="char", size=500),
	    'date':fields.date('Date'),
            'amount_total': fields.function(_amount_all, string='Total', digits_compute= dp.get_precision('Total')),
            'prepare_date':fields.date('Date',),
            'check_date':fields.date('Date',),
            'auth_date':fields.date('Date',),
            'receive_date':fields.date('Date',),
           }
           
   
cheque_payment()

class Cheque_payment_line(osv.osv):

    _name = "cheque.payment.line"
    
    def _tot_amount(self, cr, uid, ids, field_names, args, context=None):
    	res = {}
	for line in self.browse(cr, uid, ids, context=context):
           
            res[line.id] = line.price_unit * line.qty
        return res

    def product_id_change(self, cr, uid, ids, product,context=None):
	v = {}
	price = 0.0
	if product:
		price = self.pool.get('product.product').browse(cr,uid,product).list_price
		
        v['price_unit'] = price
	return {'value': v}
    
    
    
    
    _columns = {
            'qty': fields.float('Qty'),
            'name':fields.many2one('product.product','Description/Details/Purpose',required=True),
            'price_unit': fields.float('Unit Price(KES)'),
            'amount': fields.function(_tot_amount,'Amount(KES)'),
            'cheque_id':fields.many2one('cheque.payment.voucher', 'Voucher_id'),
    
            
           }
   
Cheque_payment_line()
