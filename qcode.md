qcode
---
```py
	def _printQR(self, mat):
		for i in mat:
			BLACK = '\033[40m  \033[0m'
			WHITE = '\033[47m  \033[0m'
			print ''.join([BLACK if j else WHITE for j in i])

	def _str2qr(self, str):
		qr = qrcode.QRCode()
		qr.border = 1
		qr.add_data(str)
		mat = qr.get_matrix()
		self._printQR(mat) # qr.print_tty() or qr.print_ascii()
```
