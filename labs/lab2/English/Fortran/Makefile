FCOPTS = -fast -ta=tesla -Minfo=accel

# Insert your Summit project ID here, then uncomment the line
#PROJECT =

LSFRUN = bsub -W 5 -nnodes 1 -P $(ALLOC) -Is jsrun -n1 -a1 -c10 -g1 --smpiargs="none"

.PHONY: all
all: clean laplace run

laplace:
        pgfortran $(FCOPTS) -c laplace2d.f90
        pgfortran $(FCOPTS) -c jacobi.f90
        pgfortran $(FCOPTS) -o laplace.x jacobi.o laplace2d.o

clean:
        -rm jacobi.mod jacobi.o laplace2d.o
        -rm laplace.x

chkalloc:
ifndef PROJECT
        $(error Please edit the Makefile and provide your Summit project ID)
else
        @echo "Using project $(PROJECT)"
endif

pgaccelinfo: chkalloc
        $(LSFRUN) pgaccelinfo

run: chkalloc
        $(LSFRUN) ./laplace.x

profile: chkalloc
        $(LSFRUN) nv-nsight-cu-cli ./laplace.x
