FROM ubuntu:xenial
RUN apt-get update && apt-get install -y \
software-properties-common \
apt-transport-https && \
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9 && \
add-apt-repository 'deb [arch=amd64,i386] https://cran.rstudio.com/bin/linux/ubuntu xenial/' && \
apt-get -qq update && \
apt-get install -y --allow-unauthenticated \
git \
cmake \
libjpeg-turbo8-dev \
libtiff-dev \
libgdiplus \
libfftw3-dev \
libx11-dev \
build-essential \
libopenblas-dev \
liblapack-dev \
libopencv-dev \
libatlas-base-dev \
libcairo2-dev \
libcurl4-openssl-dev \
libssl-dev \
libxml2-dev \
libxt-dev \
r-base \
r-base-dev
RUN Rscript -e "install.packages('devtools', repo = 'https://cran.rstudio.com')" && \
cd ..

RUN git clone --recursive https://github.com/apache/incubator-mxnet mxnet
#RUN cd /mxnet/ && sed -i 's/sudo //g' Makefile
#RUN cd /mxnet/ && make -j $(nproc) USE_OPENCV=1 USE_BLAS=atlas
RUN cd /mxnet/ && make USE_OPENCV=1 USE_BLAS=atlas

RUN Rscript -e "install.packages('devtools', repo = 'https://cran.rstudio.com')" && \
cd ..

RUN cd /mxnet && mkdir -p R-package/inst/libs && \
echo "moving required files" && \
cp src/io/image_recordio.h R-package/src && \
cp -rf lib/libmxnet.so R-package/inst/libs && \
if [ -e "lib/libmkldnn.so.0" ]; then \
	cp -rf lib/libmkldnn.so.0 R-package/inst/libs; \
	cp -rf lib/libiomp5.so R-package/inst/libs; \
	cp -rf lib/libmklml_intel.so R-package/inst/libs; \
fi && \
if [ -e "lib/libsparse_matrix.so" ]; then \
	cp -rf lib/libsparse_matrix.so R-package/inst/libs; \
fi

RUN cd /mxnet && mkdir -p R-package/inst/include && \
cp -rl include/* R-package/inst/include && \
Rscript -e "install.packages('devtools',lib = '/usr/local/lib/R/site-library', repo = 'https://cran.rstudio.com')" && \
Rscript -e " devtools::install_version('roxygen2',version='6.1.1',quiet=TRUE,lib = '/usr/local/lib/R/site-library', repo = 'https://cran.rstudio.com')" && \
Rscript -e "library(devtools); library(methods); options(repos=c(CRAN='https://cran.rstudio.com')); install_deps(pkg='R-package', dependencies = TRUE, upgrade='always')"

RUN cd /mxnet && \
cp R-package/dummy.NAMESPACE R-package/NAMESPACE && \
echo "import(Rcpp)" >> R-package/NAMESPACE && \
R CMD INSTALL R-package && \
Rscript -e "require(mxnet); mxnet:::mxnet.export('R-package'); warnings()" && \
rm R-package/NAMESPACE && \
Rscript -e "devtools::document('R-package');warnings()" && \
R CMD INSTALL --build --no-multiarch R-package