import numpy as np
import cv2
import os

image_x = 200
image_y = 100
image_w = 200
image_h = 200
pic_no = 0
total_pic = 1200
flag_capturing = False

if not os.path.exists('signs'):
	os.mkdir('signs')

sign = input("Enter Sign ")

if not os.path.exists('signs/' + sign):
	os.mkdir('signs/' + sign) 

save_path = 'signs/' + sign
images = []

vc = cv2.VideoCapture(0)

while True:
	rval, frame = vc.read()

	if not rval:
		continue

	frame = cv2.flip(frame, 1)
	cv2.rectangle(frame, (image_x,image_y), (image_x + image_w,image_y + image_h), (0,255,0), 2)
	

	hand = frame[image_y:image_y+image_h, image_x:image_x+image_w]
	hand = cv2.cvtColor(hand, cv2.COLOR_BGR2GRAY)
	blur = cv2.GaussianBlur(hand, (11,11), 0)
	blur = cv2.medianBlur(blur, 15)
	thresh = cv2.threshold(blur,215,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU)[1]
	thresh = cv2.bitwise_not(thresh)

	if flag_capturing:
		pic_no += 1
		img = cv2.resize( thresh, (50,50) )
		images.append( np.array(img) )
		cv2.putText(frame, "Capturing "+str(pic_no)+'/'+str(total_pic), (30, 40), cv2.FONT_HERSHEY_TRIPLEX, 1, (255, 255, 0))

	cv2.imshow("image", frame)
	cv2.imshow("hand", thresh)

	keypress = cv2.waitKey(1)

	if pic_no == total_pic:
		flag_capturing = False
		break

	if keypress == ord('q'):
		break
	elif keypress == ord('c'):
		flag_capturing = True

vc.release()
cv2.destroyAllWindows()

print ("Saving Images ... ")
for i in range( len(images) ):
	cv2.imwrite(save_path + "/" + str(i) + ".jpg", img)

		

