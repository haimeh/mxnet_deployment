# Use haimeh custom (up to date) mxnet Dockerfile to generate the installable package
#FROM haimeh/mxnet_r AS mx
FROM mxnetr_build_opencpu AS mx
# Use the official OpenCPU Dockerfile as a base
FROM opencpu/devel
RUN apt-get update && apt-get install -y \
	build-essential \
	libopenblas-dev \
	liblapack-dev \
	libopencv-dev \
	libxt-dev \
	libatlas-base-dev \
	libcairo2-dev \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    libxt-dev \
	r-base-dev
#COPY --from=mx /mxnet/R-package/ /mxnet/mxnet_*_R_x86_64-pc-linux-gnu.tar.gz /mxnet/R-package/
COPY --from=mx /mxnet/R-package/ /mxnet/R-package
COPY --from=mx /mxnet/mxnet_*_R_x86_64-pc-linux-gnu.tar.gz /mxnet
RUN dir /mxnet && cd /mxnet && \
Rscript -e "install.packages('devtools',lib = '/usr/local/lib/R/site-library', repo = 'https://cran.rstudio.com')" && \
Rscript -e "library(devtools); library(methods); options(repos=c(CRAN='https://cran.rstudio.com')); install_deps(pkg='R-package', dependencies = TRUE, upgrade='always')"
RUN cd /mxnet && R CMD INSTALL mxnet_*_R_x86_64-pc-linux-gnu.tar.gz
#RUN cd /mxnet && mv R-package mxnetR && R CMD INSTALL mxnetR