CC=g++-6

TARGET=vt100sim

LIBS=-lncurses

OBJS=main.o nvr.o keyboard.o vt100sim.o \
	8080/simglb.o \
	8080/sim1a.o \
	8080/simint.o \
	pusart.o

%.o: %.c
	gcc-6 -c$(CFLAGS) $(CPPFLAGS) $< -o $@ 
   
%.o: %.cpp
	$(CC) -c$(CFLAGS) $(CPPFLAGS) $< -o $@ 
   
all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) -o  $(TARGET) $^ $(LIBS)

$(OBJS): keyboard.h nvr.h optionparser.h pusart.h vt100sim.h

clean:
	@-rm -f $(OBJS) $(TARGET)

wide:
	$(MAKE) all CXXFLAGS=-I/usr/include/ncursesw LIBS=-lncursesw
