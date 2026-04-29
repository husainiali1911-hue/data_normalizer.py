import csv
import re

def normalize_name(name):
    """Bersihkan dan kapitalisasi nama."""
    if not name or name.strip() == '':
        return 'N/A'
    return name.strip().title()

def validate_email(email):
    """Validasi format email sederhana."""
    if not email or email.strip() == '':
        return 'N/A'
    email = email.strip().lower()
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    if re.match(pattern, email):
        return email
    else:
        return 'INVALID'

def normalize_phone(phone):
    """Standarisasi nomor telepon ke format +62."""
    if not phone or phone.strip() == '':
        return 'N/A'
    # Hapus semua karakter kecuali angka dan +
    cleaned = re.sub(r'[^\d+]', '', phone)
    if not cleaned:
        return 'N/A'
    # Jika dimulai dengan 0, ganti +62
    if cleaned.startswith('0'):
        cleaned = '+62' + cleaned[1:]
    # Jika dimulai dengan 62 tanpa +
    elif cleaned.startswith('62') and not cleaned.startswith('+'):
        cleaned = '+' + cleaned
    # Jika hanya angka tanpa kode negara, tambahkan +62
    elif cleaned.isdigit() and not cleaned.startswith('62'):
        cleaned = '+62' + cleaned
    return cleaned

def normalize_address(address):
    """Isi alamat kosong dengan N/A."""
    if not address or address.strip() == '':
        return 'N/A'
    return address.strip()

def main():
    input_file = 'raw_data.csv'
    output_file = 'normalized_data.csv'
    
    with open(input_file, 'r') as infile, open(output_file, 'w', newline='') as outfile:
        reader = csv.DictReader(infile)
        fieldnames = ['name', 'email', 'phone', 'address']
        writer = csv.DictWriter(outfile, fieldnames=fieldnames)
        writer.writeheader()
        
        for row in reader:
            normalized = {
                'name': normalize_name(row.get('name', '')),
                'email': validate_email(row.get('email', '')),
                'phone': normalize_phone(row.get('phone', '')),
                'address': normalize_address(row.get('address', ''))
            }
            writer.writerow(normalized)
    
    print(f"Normalisasi selesai. Output: {output_file}")

if __name__ == '__main__':
    main()
