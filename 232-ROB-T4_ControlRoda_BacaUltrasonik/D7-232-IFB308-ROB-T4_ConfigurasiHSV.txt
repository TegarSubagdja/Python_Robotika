import cvzone
from cvzone.ColorModule import ColorFinder
import cv2
import socket

# Fungsi callback untuk set trackbar values
def setValues(trackbarVals):
    global hsvVals
    hsvVals = {'hmin': trackbarVals[0], 'smin': trackbarVals[1], 'vmin': trackbarVals[2],
               'hmax': trackbarVals[3], 'smax': trackbarVals[4], 'vmax': trackbarVals[5]}
    save_hsv_vals(hsvVals, 'hsv_vals.txt')

# Fungsi untuk menyimpan data hsvVals ke dalam file teks
def save_hsv_vals(hsv_vals, filename):
    with open(filename, 'w') as file:
        file.write("hsvVals = {\n")
        for key, value in hsv_vals.items():
            file.write(f"    '{key}': {value},\n")
        file.write("}\n")

cap = cv2.VideoCapture(0)
cap.set(3, 1280)
cap.set(4, 720)

success, img = cap.read()
h, w, _ = img.shape

myColorFinder = ColorFinder(False)
hsvVals = {'hmin': 0, 'smin': 0, 'vmin': 0, 'hmax': 179, 'smax': 255, 'vmax': 255}
save_hsv_vals(hsvVals, 'hsv_vals.txt')

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
serverAddressPort = ("127.0.0.1", 5053)

# Membuat jendela baru dan menambahkan trackbar
cv2.namedWindow("Trackbars")
cv2.resizeWindow("Trackbars", 640, 240)
cv2.createTrackbar("Hue Min", "Trackbars", hsvVals['hmin'], 179, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))
cv2.createTrackbar("Saturation Min", "Trackbars", hsvVals['smin'], 255, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))
cv2.createTrackbar("Value Min", "Trackbars", hsvVals['vmin'], 255, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))
cv2.createTrackbar("Hue Max", "Trackbars", hsvVals['hmax'], 179, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))
cv2.createTrackbar("Saturation Max", "Trackbars", hsvVals['smax'], 255, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))
cv2.createTrackbar("Value Max", "Trackbars", hsvVals['vmax'], 255, lambda x: setValues([cv2.getTrackbarPos("Hue Min", "Trackbars"), cv2.getTrackbarPos("Saturation Min", "Trackbars"), cv2.getTrackbarPos("Value Min", "Trackbars"), cv2.getTrackbarPos("Hue Max", "Trackbars"), cv2.getTrackbarPos("Saturation Max", "Trackbars"), cv2.getTrackbarPos("Value Max", "Trackbars")]))


while True:
    success, img = cap.read()
    imgColor, mask = myColorFinder.update(img, hsvVals)
    imgContour, contours = cvzone.findContours(img, mask)

    if contours:
        data = contours[0]['center'][0], \
               h - contours[0]['center'][1], \
               int(contours[0]['area'])
        print(data)
        sock.sendto(str.encode(str(data)), serverAddressPort)

    # imgStack = cvzone.stackImages([img, imgColor, mask, imgContour], 2, 0.5)
    # cv2.imshow("Image", imgStack)
    imgContour = cv2.resize(imgContour, (0, 0), None, 0.5, 0.5)
    cv2.imshow("ImageContour", imgColor)
    cv2.waitKey(1)
