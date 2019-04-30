# Use haimeh custom (up to date) mxnet Dockerfile to generate the installable package
FROM haimeh/mxnet_r AS mx
# Use the official OpenCPU Dockerfile as a base
FROM opencpu/base
COPY --from=mx /mxnet/R-package/ /mxnet/mxnet_*_R_x86_64-pc-linux-gnu.tar.gz /mxnet/R-package/
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
    libxt-dev && \
dir /mxnet/R-package && \
cd /mxnet/R-package && Rscript -e "install.packages('devtools', lib = '/usr/local/lib/R/site-library', repo = 'https://cran.rstudio.com')" && \
cd /mxnet && Rscript -e "library(devtools); options(repos=c(CRAN='https://cran.rstudio.com')); install_deps(pkg='R-package', dependencies = TRUE, upgrade='always', lib = '/usr/local/lib/R/site-library',)" && \
cd /mxnet/R-package && R CMD INSTALL mxnet_*_R_x86_64-pc-linux-gnu.tar.gz