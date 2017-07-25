CROSS_COMPILE=arm-hisiv300-linux-
CC=$(CROSS_COMPILE)gcc

demo:libprint.so main.c
	$(CC) main.c -lprint -L. -o $@

libprint.so:libprint.c
	$(CC) --shared $^ -o $@

clean:
	rm -fr demo libprint.so
