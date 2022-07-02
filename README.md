{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "name": "#TASK_7 .ipynb",
      "provenance": [],
      "collapsed_sections": [],
      "include_colab_link": true
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    }
  },
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/Aman-Khandwe/The-Spark-foundation-Internship/blob/main/_TASK_7_.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "# **GRIP @ THE SPARKS FOUNDATION**\n",
        "# **Name- Aman Khandwe**\n",
        "# **Batch- JUNE 2022**"
      ],
      "metadata": {
        "id": "gp8uMHxxk5WZ"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "# **TASK-7 Stock Market Prediction using Numerical and Textual Analysis**\n",
        "(Level - Advanced)\n",
        "\n",
        "\n",
        "* Create a hybrid model for stock price/performance prediction using numerical analysis of historical stock prices, and sentimental analysis of news headlines.\n",
        "* Stock to analyze and predict SENSEX (S&P BSE SENSEX).\n",
        "* Use either R or Python, or both for separate analysis and then combine the findings to create a hybrid model."
      ],
      "metadata": {
        "id": "jfpQKqZYk-Ba"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Importing Librarires*"
      ],
      "metadata": {
        "id": "lm8R5n-NlXOb"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "KkPUVvNX8cyE",
        "outputId": "eba09c28-34e3-4da4-91f6-852de1cbd269",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 85
        }
      },
      "source": [
        "import os\n",
        "import warnings\n",
        "warnings.filterwarnings('ignore')\n",
        "import pandas as pd\n",
        "import numpy as np\n",
        "import matplotlib.pyplot as plt\n",
        "import itertools\n",
        "from statsmodels.tsa.stattools import adfuller, acf, pacf\n",
        "from statsmodels.tsa.arima_model import ARIMA\n",
        "import nltk\n",
        "import re\n",
        "from nltk.corpus import stopwords\n",
        "nltk.download('stopwords')\n",
        "nltk.download('vader_lexicon')\n",
        "from textblob import TextBlob\n",
        "from nltk.sentiment.vader import SentimentIntensityAnalyzer\n",
        "from nltk.stem.porter import PorterStemmer\n",
        "from sklearn.metrics import mean_squared_error\n",
        "from sklearn.model_selection import train_test_split\n",
        "from sklearn.ensemble import RandomForestRegressor, AdaBoostRegressor\n",
        "import xgboost \n",
        "import lightgbm "
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "[nltk_data] Downloading package stopwords to /root/nltk_data...\n",
            "[nltk_data]   Package stopwords is already up-to-date!\n",
            "[nltk_data] Downloading package vader_lexicon to /root/nltk_data...\n",
            "[nltk_data]   Package vader_lexicon is already up-to-date!\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "1xRNMsEreplR",
        "outputId": "fe75939a-bfaa-4b44-d64b-30fac527216a",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 701
        }
      },
      "source": [
        "!pip install pmdarima"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Collecting pmdarima\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/be/62/725b3b6ae0e56c77534de5a8139322e7b863ca53fd5bd6bd3b7de87d0c20/pmdarima-1.7.1-cp36-cp36m-manylinux1_x86_64.whl (1.5MB)\n",
            "\u001b[K     |████████████████████████████████| 1.5MB 2.4MB/s \n",
            "\u001b[?25hRequirement already satisfied: urllib3 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (1.24.3)\n",
            "Requirement already satisfied: scipy>=1.3.2 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (1.4.1)\n",
            "Collecting statsmodels<0.12,>=0.11\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/cb/83/540fd83238a18abe6c2d280fa8e489ac5fcefa1f370f0ca1acd16ae1b860/statsmodels-0.11.1-cp36-cp36m-manylinux1_x86_64.whl (8.7MB)\n",
            "\u001b[K     |████████████████████████████████| 8.7MB 6.9MB/s \n",
            "\u001b[?25hRequirement already satisfied: pandas>=0.19 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (1.1.2)\n",
            "Requirement already satisfied: joblib>=0.11 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (0.16.0)\n",
            "Requirement already satisfied: scikit-learn>=0.22 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (0.22.2.post1)\n",
            "Collecting setuptools<50.0.0\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/c3/a9/5dc32465951cf4812e9e93b4ad2d314893c2fa6d5f66ce5c057af6e76d85/setuptools-49.6.0-py3-none-any.whl (803kB)\n",
            "\u001b[K     |████████████████████████████████| 808kB 47.9MB/s \n",
            "\u001b[?25hRequirement already satisfied: numpy>=1.17.3 in /usr/local/lib/python3.6/dist-packages (from pmdarima) (1.18.5)\n",
            "Collecting Cython<0.29.18,>=0.29\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/e7/d7/510ddef0248f3e1e91f9cc7e31c0f35f8954d0af92c5c3fd4c853e859ebe/Cython-0.29.17-cp36-cp36m-manylinux1_x86_64.whl (2.1MB)\n",
            "\u001b[K     |████████████████████████████████| 2.1MB 40.2MB/s \n",
            "\u001b[?25hRequirement already satisfied: patsy>=0.5 in /usr/local/lib/python3.6/dist-packages (from statsmodels<0.12,>=0.11->pmdarima) (0.5.1)\n",
            "Requirement already satisfied: python-dateutil>=2.7.3 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.19->pmdarima) (2.8.1)\n",
            "Requirement already satisfied: pytz>=2017.2 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.19->pmdarima) (2018.9)\n",
            "Requirement already satisfied: six in /usr/local/lib/python3.6/dist-packages (from patsy>=0.5->statsmodels<0.12,>=0.11->pmdarima) (1.15.0)\n",
            "\u001b[31mERROR: datascience 0.10.6 has requirement folium==0.2.1, but you'll have folium 0.8.3 which is incompatible.\u001b[0m\n",
            "Installing collected packages: statsmodels, setuptools, Cython, pmdarima\n",
            "  Found existing installation: statsmodels 0.10.2\n",
            "    Uninstalling statsmodels-0.10.2:\n",
            "      Successfully uninstalled statsmodels-0.10.2\n",
            "  Found existing installation: setuptools 50.3.0\n",
            "    Uninstalling setuptools-50.3.0:\n",
            "      Successfully uninstalled setuptools-50.3.0\n",
            "  Found existing installation: Cython 0.29.21\n",
            "    Uninstalling Cython-0.29.21:\n",
            "      Successfully uninstalled Cython-0.29.21\n",
            "Successfully installed Cython-0.29.17 pmdarima-1.7.1 setuptools-49.6.0 statsmodels-0.11.1\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "application/vnd.colab-display-data+json": {
              "pip_warning": {
                "packages": [
                  "pkg_resources",
                  "statsmodels"
                ]
              }
            }
          },
          "metadata": {
            "tags": []
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "PQEPCV-tZ-lJ"
      },
      "source": [
        "TIME SERIES ANALYSIS"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "v7tRx9Yz8rUT",
        "outputId": "a1ecec19-5a82-4472-9f9d-e8e82812bd6f",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 170
        }
      },
      "source": [
        "df_prices = pd.read_csv('BSESN.csv')\n",
        "print(df_prices.head())\n",
        "print(df_prices.size)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "         Date          Open          High  ...         Close     Adj Close   Volume\n",
            "0  2015-01-02  27521.279297  27937.470703  ...  27887.900391  27887.900391   7400.0\n",
            "1  2015-01-05  27978.429688  28064.490234  ...  27842.320313  27842.320313   9200.0\n",
            "2  2015-01-06  27694.230469  27698.929688  ...  26987.460938  26987.460938  14100.0\n",
            "3  2015-01-07  26983.429688  27051.599609  ...  26908.820313  26908.820313  12200.0\n",
            "4  2015-01-08  27178.769531  27316.410156  ...  27274.710938  27274.710938   8200.0\n",
            "\n",
            "[5 rows x 7 columns]\n",
            "8596\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Converting Date column to datetime datatype*"
      ],
      "metadata": {
        "id": "vSj8dfQnllli"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "cWlvGON28uJP",
        "outputId": "d04a14fc-7f09-47a3-9894-72fd6da680d0",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 255
        }
      },
      "source": [
        "df_prices['Date'] = pd.to_datetime(df_prices['Date'])\n",
        "df_prices.info()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "<class 'pandas.core.frame.DataFrame'>\n",
            "RangeIndex: 1228 entries, 0 to 1227\n",
            "Data columns (total 7 columns):\n",
            " #   Column     Non-Null Count  Dtype         \n",
            "---  ------     --------------  -----         \n",
            " 0   Date       1228 non-null   datetime64[ns]\n",
            " 1   Open       1224 non-null   float64       \n",
            " 2   High       1224 non-null   float64       \n",
            " 3   Low        1224 non-null   float64       \n",
            " 4   Close      1224 non-null   float64       \n",
            " 5   Adj Close  1224 non-null   float64       \n",
            " 6   Volume     1224 non-null   float64       \n",
            "dtypes: datetime64[ns](1), float64(6)\n",
            "memory usage: 67.3 KB\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "LLeJDfJqeljf"
      },
      "source": [
        "df_prices.dropna(inplace = True)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "AN2mEP8b9Hhq",
        "outputId": "5caa7c33-e17c-44bb-ad42-f6163484f6e9",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 393
        }
      },
      "source": [
        "plt.figure(figsize=(10, 6))\n",
        "df_prices['Close'].plot()\n",
        "plt.ylabel('Close')"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "Text(0, 0.5, 'Close')"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 5
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Plotting moving average*"
      ],
      "metadata": {
        "id": "rPK0VzSdltRz"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "7Rc5uD5791HV",
        "outputId": "ba7082a0-ef8f-429c-ddc8-939c6e926146",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 391
        }
      },
      "source": [
        "close = df_prices['Close']\n",
        "ma = close.rolling(window = 50).mean()\n",
        "std = close.rolling(window = 50).std()\n",
        "\n",
        "plt.figure(figsize=(10, 6))\n",
        "df_prices['Close'].plot(color = 'b', label = 'Close')\n",
        "ma.plot(color = 'r', label = 'Rolling Mean')\n",
        "std.plot(label = 'Rolling Standard Deviation')\n",
        "plt.legend()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "<matplotlib.legend.Legend at 0x7f22acd52320>"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 6
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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iYiIiHhEQUxERETEIwpiIiIiIh5REBMRERHxiIKYiIiIiEcUxEREREQ8oiAmIiIi4hEFMRERERGPKIiJiIiIeERBTERERMQjCmIiIiIiHlEQExEREfGIgpiIiIiIRxTERERERDyiICYiIiLiEQUxEREREY8oiImIiIh4REFMRERExCMKYiIiIiIeURATERER8YiCmIiIiIhHFMREREREPKIgJiIiIuIRBTERERERjyiIiYiIiHhEQUxERETEIwpiIiIiIh5REBMRERHxiIKYiIiIiEcUxEREREQ8oiAmIiIi4hEFMRERERGPKIiJiIiIeERBTERERMQjCmIiIiIiHlEQExEREfGIgpiIiIiIRxTERERERDyiICYiIiLiEQUxEREREY8oiImIiIh4REFMRERExCMKYiIiIiIeURATERER8YiCmIiIiIhHFMREREREPKIgJiIiIuIRBTERERERjyiIiYiIiHhEQUxERETEIwpiIiIiIh5REBMRERHxiIKYiIiIiEcUxEREREQ8oiAmIiIi4hEFMRERERGPnDKIGWOWGWMajDFbEsryjTFvGmN2udd5brkxxjxmjKkyxmwyxsxNeM4Sd/ldxpglCeWXGGM2u895zBhjBvtNioiIiAxFA+kR+wWw6KiybwArrLXlwAr3PsCNQLl7WQr8BJzgBjwMXA5cBjzcG97cZe5PeN7RryUiIiIyLJ0yiFlr3wGajypeDCx3by8Hbkkof9I6VgG5xphRwA3Am9baZmttC/AmsMh9LNtau8paa4EnE/6WiIiIyLB2pnPERlhr69zb9cAI93YJcCBhuRq37GTlNccpFxERERn2znqyvtuTZQehLqdkjFlqjKk0xlQ2Njaej5cUEREROWfONIgdcocVca8b3PJaoCxhuVK37GTlpccpPy5r7ePW2gprbUVRUdEZVl1ERERkaDjTIPYS0Hvk4xLgxYTyu92jJ+cBQXcI8w1goTEmz52kvxB4w32szRgzzz1a8u6EvyUiIiIyrPlPtYAx5jfANUChMaYG5+jH7wHPGGPuA/YDt7uLvwrcBFQBXcA9ANbaZmPMd4C17nLfttb2HgDwBZwjM9OA19yLiIiIyLBnnCleF56KigpbWVnpdTVERERETskYs85aW3F0uc6sLyIiIuIRBTERERERjyiIiYiIiHhEQUxERETEIwpiIiIiIh5REBMRERHxiIKYiIiIiEcUxEREREQ8oiAmIiIi4hEFMRERERGPKIiJiIiIeERBTERERMQjCmIiIiIiHlEQExEREfGIgpiIiIiIRxTERERERDyiICYiIiLiEQUxEREREY8oiImIiIh4REFMRERExCMKYiIiIiIeURATERER8YiCmIiIiIhHFMREREREPKIgJiIiIuIRBTERERERjyiIiYiIiHhEQUxERETEIwpiIiIiIh5REBMRERHxiIKYiIiIiEcUxEREREQ8oiAmIiIi4hEFMRERERGPKIiJiIiIeERBTERERMQjCmIiIiIiHlEQExEREfGIgpiIiIiIRxTERERERDyiICYiIiLiEQUxEREREY8oiImIiIh4REFMRERExCMKYiIiIiIeURATERER8YiCmIiIiIhHFMREREREPKIgJiIiIuIRBTERERERjyiIiYiIiHhEQUxERETEI36vKyAiInIh6AxF+eMH9XSGYsyfWMDEokyvqyTDgIKYiIjIKby88SAPvbCZ9lC0r+zK8kJ+cOssRuakelgzudBpaFJEROQknly5jy89vYEpI7N47vPzefdr1/KNG6eybn8Lt/z4r2yvb/O6inIBUxATERE5jp5IjEde28b/fnEr108dwa/+++VUjMunLD+dz189kRe+cAUWy20/XcnK3U1eV1cuUMZa63UdzkhFRYWtrKz0uhoiIjIM1bR0cd8vKtlxqJ07LyvjO4un4/cd23dR29rNkmVrqG7q4kd3zeGGaSM9qO3JWWsJdkfYUd/OjkPtbKtrJxaPs2j6SK6dUowxxusqnrX3qg7zbtVh3treQHVzF9ZCYVYyBRkpzB2Tx9cWTSE14PO0jsaYddbaimPKFcTkdMXjlsr9Lby9s4HdDZ20dofpicS5aFQ2i6aP5MpJhSQlXfgfbBEZPhrae3jp/YPUBXvITQuwoLyQ2WW5xw0hPZEYd/7HKqoOdfCju+ZwzZTik/7t1q4wn31iLR/UtfGjO4dOGNtU08pjK3axZm8zbT1H5rblpAUwBlq7IvzdnBK++4kZpCWf35ASisZI8Z/9a3aFozz84laeXVeDP8kwd2weM0tysEBTR4j6th5W721mwaRClt9zmafbJgUxGRRVDR3c/2Qlew934k8yjClIpyAjGX9SEltqg7SHomSn+hlbkMFVkwu57ZIyxhVmeF3tPvubOmls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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Plotting returns*"
      ],
      "metadata": {
        "id": "s9JUoOgIlxUL"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Nh8DfBr69-VR",
        "outputId": "477105ac-c895-4cee-85b7-fd5b72c62879",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 407
        }
      },
      "source": [
        "returns = close / close.shift(1) - 1\n",
        "\n",
        "plt.figure(figsize = (10,6))\n",
        "returns.plot(label='Return', color = 'g')\n",
        "plt.title(\"Returns\")"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "Text(0.5, 1.0, 'Returns')"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 7
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "qdIQZfYRWhVV"
      },
      "source": [
        "train = df_prices[:1000]\n",
        "test = df_prices[1000:]"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Stationarity test*"
      ],
      "metadata": {
        "id": "xufbUPLDl4zK"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "blcNOhntWmwI",
        "outputId": "d77d9958-d864-46d2-d728-1aabde76bfa3",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 620
        }
      },
      "source": [
        "def test_stationarity(timeseries):\n",
        " rolmean = timeseries.rolling(20).mean()\n",
        " rolstd = timeseries.rolling(20).std()\n",
        "\n",
        " plt.figure(figsize = (10,8))\n",
        " plt.plot(timeseries, color = 'y', label = 'original')\n",
        " plt.plot(rolmean, color = 'r', label = 'rolling mean')\n",
        " plt.plot(rolstd, color = 'b', label = 'rolling std')\n",
        " plt.xlabel('Date')\n",
        " plt.legend()\n",
        " plt.title('Rolling Mean and Standard Deviation',  fontsize = 20)\n",
        " plt.show(block = False)\n",
        " \n",
        " print('Results of dickey fuller test')\n",
        " result = adfuller(timeseries, autolag = 'AIC')\n",
        " labels = ['ADF Test Statistic','p-value','#Lags Used','Number of Observations Used']\n",
        " for value,label in zip(result, labels):\n",
        "   print(label+' : '+str(value) )\n",
        " if result[1] <= 0.05:\n",
        "   print(\"Strong evidence against the null hypothesis(Ho), reject the null hypothesis. Data is stationary\")\n",
        " else:\n",
        "   print(\"Weak evidence against null hypothesis, time series is non-stationary \")\n",
        "test_stationarity(train['Close'])"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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iIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiIhLFMREREREXKIgJiIiIuISBTERERERlyiIiYiIiLhEQUxERETEJQpiIiIiIi5REBMRERFxiYKYiIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXbDeIGWPGGWOKjDHzmpTdZYzJN8bMDn2NabLvVmNMjjFmsTHm2Cblx4XKcowxf2lS3t0YMyVU/pYxxrcnH1BERESkvdqRFrHXgONaKH/CWjsk9PUZgDFmAHAWMDB0znPGGI8xxgM8CxwPDADODh0L8FDoWr2ADcDFu/NAIiIiInuL7QYxa+0PQMkOXu9kYIK1NmCtXQHkACNCXznW2uXW2hpgAnCyMcYAvwHeDZ0/HjhlJ59BREREZK+0O33ErjHGzAm9ukwKlXUC8pocszpUtrXyFKDUWlu3WbmIiIjIPm9Xg9jzQE9gCLAGeGyP1WgbjDGXGWOmG2OmFxcXt8UtRURERFrNLgUxa+1aa23QWlsPvIzz6hEgH8hqcmjnUNnWytcDicYY72blW7vvS9babGttdlpa2q5UXURERKTd2KUgZozJaPLxVKBhROXHwFnGmAhjTHegNzAVmAb0Do2Q9OF06P/YWmuB74DTQ+ePBT7alTqJiIiI7G282zvAGPMmcDiQaoxZDdwJHG6MGQJYYCVwOYC1dr4x5m1gAVAHXG2tDYaucw3wJeABxllr54du8WdggjHmPmAW8OoeezoRERGRdsw4jVJ7n+zsbDt9+nS3qyEiIiKyXcaYGdba7M3LNbO+iIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXKIiJiIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiIhLFMREREREXKIgJiIiIuISBTERERERlyiIiYiIiLhEQUxERETEJQpiIiIiIi5REBMRERFxiYKYiIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXKIiJiIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiIhLFMREREREXLLdIGaMGWeMKTLGzGtSlmyMmWiMWRr6MylUbowxTxljcowxc4wxw5qcMzZ0/FJjzNgm5QcYY+aGznnKGGP29EOKiIiItEc70iL2GnDcZmV/Ab6x1vYGvgl9Bjge6B36ugx4HpzgBtwJjARGAHc2hLfQMZc2OW/ze4mIiIjsk7YbxKy1PwAlmxWfDIwPbY8HTmlS/rp1TAYSjTEZwLHARGttibV2AzAROC60L95aO9laa4HXm1xLREREZJ+2q33EOlpr14S2C4GOoe1OQF6T41aHyrZVvrqFchEREZF93m531g+1ZNk9UJftMsZcZoyZboyZXlxc3Ba3FBEREWk1uxrE1oZeKxL6syhUng9kNTmuc6hsW+WdWyhvkbX2JWtttrU2Oy0tbRerLiIiItI+7GoQ+xhoGPk4FvioSfn5odGTo4CNoVeYXwLHGGOSQp30jwG+DO0rM8aMCo2WPL/JtURERET2ad7tHWCMeRM4HEg1xqzGGf34IPC2MeZiIBc4I3T4Z8AYIAeoAi4EsNaWGGPuBaaFjrvHWtswAOAqnJGZUcDnoS8RERGRfZ5xunjtfbKzs+306dPdroaIiIjIdhljZlhrszcv18z6IiIiIi5REBMRERFxiYKYiIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXKIiJiIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiIhLFMREREREXKIgJiIiIuISBTERERERlyiIiYiIiLhEQUxERETEJQpiIiIiIi5REBMRERFxiYKYiIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXKIiJiIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERccluBTFjzEpjzFxjzGxjzPRQWbIxZqIxZmnoz6RQuTHGPGWMyTHGzDHGDGtynbGh45caY8bu3iOJiIiI7B32RIvYEdbaIdba7NDnvwDfWGt7A9+EPgMcD/QOfV0GPA9OcAPuBEYCI4A7G8KbiIiIyL6sNV5NngyMD22PB05pUv66dUwGEo0xGcCxwERrbYm1dgMwETiuFeolIiIi0q7sbhCzwFfGmBnGmMtCZR2ttWtC24VAx9B2JyCvybmrQ2VbKxcRERHZp3l38/yDrbX5xpgOwERjzKKmO6211hhjd/MejUJh7zKALl267KnLioiIiLhit1rErLX5oT+LgA9w+nitDb1yJPRnUejwfCCryemdQ2VbK2/pfi9Za7OttdlpaWm7U3URERER1+1yEDPGxBhj4hq2gWOAecDHQMPIx7HAR6Htj4HzQ6MnRwEbQ68wvwSOMcYkhTrpHxMqExEREdmn7c6ryY7AB8aYhuv821r7hTFmGvC2MeZiIBc4I3T8Z8AYIAeoAi4EsNaWGGPuBaaFjrvHWluyG/USERER2SsYa/dYF642lZ2dbadPn+52NURERES2yxgzo8lUX400s76IiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiIhLFMREREREXKIgJiIiIuISBTERERERlyiIiYiIiLhEQUxERETEJQpiIiIiIi5REBMRERFxiYKYiIiIiEsUxERERERcoiAmIiIi4hIFMRERERGXKIiJiIiIuERBTERERMQlCmIiIiIiLlEQExEREXGJgpiIiIiISxTERERERFyiICYiIiLiEgUxEREREZcoiImIiIi4REFMRERExCUKYiIiIiIuURATERERcYmCmIiIiLSJlSvhj3+E886DFSvcrk374HW7AiIiIrLvy82FQYOgosL5nJMDP/8MxrhbL7epRUzazIYN8MADcMwxcNddUFbmdo1ERKSt3HWXE8Kefhqefx4mT4YXXnC7Vu5TENsN9fWweDF88olCxfZ88AGkp8NttzlN0/fcA1dc4XatRER2TjAIjz4KZ54JU6a4XZu9R0UFTJgAV14J11wDl17q/FJ+1VVwyinOz9HaWrdr6Q4Fsd1QWAj9+sFJJzl/oQIBt2vU/pSWOv9g/X979x1mVXWuAfz96A5IEwYYOgqK4ohCqLaACsaeYKJBooCisZGIciEWohFzIxYEldjrlaIIKmChKQSRgEhRCCUqMEgZGKQMjDPDvPeP9xzPmcoM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\n",
            "text/plain": [
              "<Figure size 720x576 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        },
        {
          "output_type": "stream",
          "text": [
            "Results of dickey fuller test\n",
            "ADF Test Statistic : -0.5913676380957044\n",
            "p-value : 0.8729206808204099\n",
            "#Lags Used : 1\n",
            "Number of Observations Used : 998\n",
            "Weak evidence against null hypothesis, time series is non-stationary \n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "jgQvUAFiWx4c",
        "outputId": "c107f408-25cf-4a74-aa5e-1b1efc32b1cb",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 391
        }
      },
      "source": [
        "train_log = np.log(train['Close']) \n",
        "test_log = np.log(test['Close'])\n",
        "\n",
        "mav = train_log.rolling(24).mean() \n",
        "plt.figure(figsize = (10,6))\n",
        "plt.plot(train_log) \n",
        "plt.plot(mav, color = 'red') "
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "[<matplotlib.lines.Line2D at 0x7f227873d128>]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 75
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "g74WFepIW52z",
        "outputId": "c5f2b0ce-99e3-4c55-f38f-8e69a3ca395d",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 620
        }
      },
      "source": [
        "train_log.dropna(inplace = True)\n",
        "test_log.dropna(inplace = True)\n",
        "\n",
        "test_stationarity(train_log)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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lwsvvJCLLrpo77zezjvvPD75yU+ydOlS5s2bx9NPP82VV17J8ccfz80337y33De/+U1OO+00Tj75ZH7v936PXC4HwMc+9jGWLl3KiSeeyC233LK3/MyZM7nllltYsmQJCxcuZPXq1UPeb2ALlCRJo8MnPgHPPTe86zz5ZPjy/n+keM2aNdx1112cccYZ3HfffTz33HM8//zzNDQ0cOqpp3LOOecc9Oa2bNnC448/zurVq7n00ku56qqruP/++6mrq2PlypVs376defPm8ZGPfKTf5UtKSlixYgW33347l112Gc888wy1tbUce+yxfPKTn2T79u3cc889PPHEExQXF3PjjTdy9913c9111/GFL3yB2tpacrkcF1xwAS+88AKLFi0CYMKECTz77LN89atf5bbbbuMb3/jGwe/DARigJEk6gs2YMYMzzjgDgMcff5xrrrmGoqIiJk+ezLnnnsvTTz+9N4gcyOWXX04mk2H+/Pls27Zt7zo/+MEPkslkmDJlCueff/6Ay+85Hbhw4UJOPPFEjjrqKABmz57Nhg0bePzxx3nmmWc49dRTAWhvb2fSpEkA/Md//Ad33HEHPT09bNmyhZUrV+6t95VXXgnAKaecwv333592F/XLACVJ0mhwgJaiQ6WysnLY1lVaWrr378H81u6e5TOZzD7rymQy9PT0EGPk+uuv56//+q/3WW7t2rXcdtttPP3004wbN44bbriBjo6ON623qKho2H7A2D5QkiQJgLPPPpt77rmHXC5HfX09jz32GKeddtqQ1rls2TLuu+8+8vk827ZtY/ny5YNe1wUXXMC9997L9u3bAdixYwfr1q2jubmZyspKampq2LZtGz/4wQ+GVOeDYQuUJEkC4IorruDnP/85J510EiEEvvjFLzJlyhTq6uoGvc4PfOAD/PjHP2b+/PkcffTRLFmyhJqamkGta/78+Xz+85/n3e9+N/l8nuLiYr7yla9wxhlnsHjxYubOncvRRx/NsmXLBl3fgxUG08Q2WEuXLo0rVqw4bNuTJGk0W7VqFfPmzRvpahxyra2tVFVV0djYyGmnncYTTzzBlClTRrpa++jvWIQQnokxLu2vvC1QkiTpkLr44otpamqiq6uLz372s6MuPA2GAUqSJB1SQ+n3NFrZiVySJCklA5QkSVJKBihJkqSUDFCSJEkpGaAkSdJ+1dXVsWDB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\n",
            "text/plain": [
              "<Figure size 720x576 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        },
        {
          "output_type": "stream",
          "text": [
            "Results of dickey fuller test\n",
            "ADF Test Statistic : -0.5063496559707529\n",
            "p-value : 0.8907714361384154\n",
            "#Lags Used : 6\n",
            "Number of Observations Used : 993\n",
            "Weak evidence against null hypothesis, time series is non-stationary \n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "vhGmsGKyXMiL",
        "outputId": "7c0a5b74-2607-4bb8-9d2b-40fbfe2a7e2f",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 620
        }
      },
      "source": [
        "train_log_diff = train_log - mav\n",
        "train_log_diff.dropna(inplace = True)\n",
        "\n",
        "test_stationarity(train_log_diff)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x576 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        },
        {
          "output_type": "stream",
          "text": [
            "Results of dickey fuller test\n",
            "ADF Test Statistic : -6.541831419968806\n",
            "p-value : 9.305308769625531e-09\n",
            "#Lags Used : 3\n",
            "Number of Observations Used : 973\n",
            "Strong evidence against the null hypothesis(Ho), reject the null hypothesis. Data is stationary\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Using auto arima to make predictions using log data*"
      ],
      "metadata": {
        "id": "RALKFMKxl_ux"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "EUASbpXkXZt8",
        "outputId": "b26d0507-9a35-438e-f112-7aa3305a2215",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 306
        }
      },
      "source": [
        "from pmdarima import auto_arima\n",
        "model = auto_arima(train_log, trace = True, error_action = 'ignore', suppress_warnings = True)\n",
        "model.fit(train_log)\n",
        "predictions = model.predict(n_periods = len(test))\n",
        "predictions = pd.DataFrame(predictions,index = test_log.index,columns=['Prediction'])"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Performing stepwise search to minimize aic\n",
            " ARIMA(2,1,2)(0,0,0)[0] intercept   : AIC=-6695.839, Time=1.67 sec\n",
            " ARIMA(0,1,0)(0,0,0)[0] intercept   : AIC=-6700.141, Time=0.21 sec\n",
            " ARIMA(1,1,0)(0,0,0)[0] intercept   : AIC=-6701.586, Time=0.24 sec\n",
            " ARIMA(0,1,1)(0,0,0)[0] intercept   : AIC=-6701.681, Time=0.18 sec\n",
            " ARIMA(0,1,0)(0,0,0)[0]             : AIC=-6701.204, Time=0.05 sec\n",
            " ARIMA(1,1,1)(0,0,0)[0] intercept   : AIC=-6700.257, Time=0.81 sec\n",
            " ARIMA(0,1,2)(0,0,0)[0] intercept   : AIC=-6699.799, Time=0.97 sec\n",
            " ARIMA(1,1,2)(0,0,0)[0] intercept   : AIC=-6697.783, Time=0.62 sec\n",
            " ARIMA(0,1,1)(0,0,0)[0]             : AIC=-6702.851, Time=0.14 sec\n",
            " ARIMA(1,1,1)(0,0,0)[0]             : AIC=-6701.366, Time=0.23 sec\n",
            " ARIMA(0,1,2)(0,0,0)[0]             : AIC=-6700.941, Time=0.14 sec\n",
            " ARIMA(1,1,0)(0,0,0)[0]             : AIC=-6702.758, Time=0.12 sec\n",
            " ARIMA(1,1,2)(0,0,0)[0]             : AIC=-6698.902, Time=0.28 sec\n",
            "\n",
            "Best model:  ARIMA(0,1,1)(0,0,0)[0]          \n",
            "Total fit time: 5.692 seconds\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "H9gLf_tNZa05",
        "outputId": "6b79f635-cdc6-4008-eb4d-bb200a955842",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 312
        }
      },
      "source": [
        "plt.plot(train_log, label='Train')\n",
        "plt.plot(test_log, label='Test')\n",
        "plt.plot(predictions, label='Prediction')\n",
        "plt.title('BSESN Stock Price Prediction')\n",
        "plt.xlabel('Time')\n",
        "plt.ylabel('Actual Stock Price')"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "Text(0, 0.5, 'Actual Stock Price')"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 79
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Calculating error*"
      ],
      "metadata": {
        "id": "YjGFcoqhmDY1"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "b4MVsxv8Zzov",
        "outputId": "a80c0c85-136a-400b-ca5a-4f6e11bbca91",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 34
        }
      },
      "source": [
        "rms = np.sqrt(mean_squared_error(test_log,predictions))\n",
        "print(\"RMSE : \", rms)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "RMSE :  0.0759730171376019\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "prGs9EPvZ6xN"
      },
      "source": [
        "**TEXTUAL ANALYSIS**"
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "LJ5gRZOIccDu",
        "outputId": "b7f3d748-a960-4108-fd0f-21c7d0919097",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "cols = ['Date','Category','News']\n",
        "df_news = pd.read_csv('india-news-headlines.csv', names = cols)\n",
        "df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>Category</th>\n",
              "      <th>News</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>publish_date</td>\n",
              "      <td>headline_category</td>\n",
              "      <td>headline_text</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>20150101</td>\n",
              "      <td>life-style.food.recipes</td>\n",
              "      <td>Breakfast recipe for diabetics: Moong idlis</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>20150101</td>\n",
              "      <td>life-style.food.recipes</td>\n",
              "      <td>Recipe: Delicious coconut balls</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>20150101</td>\n",
              "      <td>life-style.beauty</td>\n",
              "      <td>Cure that dandruff naturally</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>20150101</td>\n",
              "      <td>life-style.food.recipes</td>\n",
              "      <td>Recipe: Kerala Chicken curry</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201916</th>\n",
              "      <td>20191231</td>\n",
              "      <td>gadgets-news</td>\n",
              "      <td>vivo s1 pro with 48mp quad camera setup launch...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201917</th>\n",
              "      <td>20191231</td>\n",
              "      <td>world.pakistan</td>\n",
              "      <td>muslims mob attack gurdwara nankana sahib with...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201918</th>\n",
              "      <td>20191231</td>\n",
              "      <td>world.us</td>\n",
              "      <td>general soleimani should have been eliminated ...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201919</th>\n",
              "      <td>20191231</td>\n",
              "      <td>india</td>\n",
              "      <td>india strongly condemns vandalism at nankana s...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201920</th>\n",
              "      <td>20191231</td>\n",
              "      <td>india</td>\n",
              "      <td>pakistan pm imran khan tweets old video of vio...</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1201921 rows × 3 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "                 Date  ...                                               News\n",
              "0        publish_date  ...                                      headline_text\n",
              "1            20150101  ...        Breakfast recipe for diabetics: Moong idlis\n",
              "2            20150101  ...                    Recipe: Delicious coconut balls\n",
              "3            20150101  ...                       Cure that dandruff naturally\n",
              "4            20150101  ...                       Recipe: Kerala Chicken curry\n",
              "...               ...  ...                                                ...\n",
              "1201916      20191231  ...  vivo s1 pro with 48mp quad camera setup launch...\n",
              "1201917      20191231  ...  muslims mob attack gurdwara nankana sahib with...\n",
              "1201918      20191231  ...  general soleimani should have been eliminated ...\n",
              "1201919      20191231  ...  india strongly condemns vandalism at nankana s...\n",
              "1201920      20191231  ...  pakistan pm imran khan tweets old video of vio...\n",
              "\n",
              "[1201921 rows x 3 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 44
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "s09RrWKwEj7i",
        "outputId": "ade11429-4355-41e1-9423-7d9f48b17ea8",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 170
        }
      },
      "source": [
        "df_news.drop(0, inplace=True)\n",
        "df_news.drop('Category', axis = 1, inplace=True)\n",
        "df_news.info()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "<class 'pandas.core.frame.DataFrame'>\n",
            "Int64Index: 1201920 entries, 1 to 1201920\n",
            "Data columns (total 2 columns):\n",
            " #   Column  Non-Null Count    Dtype \n",
            "---  ------  --------------    ----- \n",
            " 0   Date    1201920 non-null  object\n",
            " 1   News    1201920 non-null  object\n",
            "dtypes: object(2)\n",
            "memory usage: 27.5+ MB\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Converting data type of Date column*"
      ],
      "metadata": {
        "id": "rCtuCT85mKSD"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "wAjXdsIhgtPD",
        "outputId": "2bda7e56-25a8-422c-c317-757c0b1cbeac",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "df_news['Date'] = pd.to_datetime(df_news['Date'],format= '%Y%m%d')\n",
        " df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>News</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Breakfast recipe for diabetics: Moong idlis</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Recipe: Delicious coconut balls</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Cure that dandruff naturally</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Recipe: Kerala Chicken curry</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>5</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Recipe: Mocha Coffee</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201916</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>vivo s1 pro with 48mp quad camera setup launch...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201917</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>muslims mob attack gurdwara nankana sahib with...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201918</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>general soleimani should have been eliminated ...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201919</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>india strongly condemns vandalism at nankana s...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1201920</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>pakistan pm imran khan tweets old video of vio...</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1201920 rows × 2 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "              Date                                               News\n",
              "1       2015-01-01        Breakfast recipe for diabetics: Moong idlis\n",
              "2       2015-01-01                    Recipe: Delicious coconut balls\n",
              "3       2015-01-01                       Cure that dandruff naturally\n",
              "4       2015-01-01                       Recipe: Kerala Chicken curry\n",
              "5       2015-01-01                               Recipe: Mocha Coffee\n",
              "...            ...                                                ...\n",
              "1201916 2019-12-31  vivo s1 pro with 48mp quad camera setup launch...\n",
              "1201917 2019-12-31  muslims mob attack gurdwara nankana sahib with...\n",
              "1201918 2019-12-31  general soleimani should have been eliminated ...\n",
              "1201919 2019-12-31  india strongly condemns vandalism at nankana s...\n",
              "1201920 2019-12-31  pakistan pm imran khan tweets old video of vio...\n",
              "\n",
              "[1201920 rows x 2 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 46
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Grouping the headlines for each day*"
      ],
      "metadata": {
        "id": "n5nXdAeamOwK"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Bt40GZW963ac",
        "outputId": "66937bcb-0735-4baf-8983-76b82523d969",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "df_news['News'] = df_news.groupby(['Date']).transform(lambda x : ' '.join(x)) \n",
        "df_news = df_news.drop_duplicates() \n",
        "df_news.reset_index(inplace = True, drop = True)\n",
        "df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>News</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>Breakfast recipe for diabetics: Moong idlis Re...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-02</td>\n",
              "      <td>Drink smart with these party tips How to say s...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-03</td>\n",
              "      <td>3 Stylish New Year cocktail recipes you'll LOV...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-04</td>\n",
              "      <td>How to get that bikini body Rules of love-maki...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-05</td>\n",
              "      <td>Recipe: Strawberry cupcakes Recipe: Kaju jeera...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1821</th>\n",
              "      <td>2019-12-27</td>\n",
              "      <td>All schools in Naintial to be closed for two d...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1822</th>\n",
              "      <td>2019-12-28</td>\n",
              "      <td>Shivin Narang on Bigg Boss 13 contestants: It'...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1823</th>\n",
              "      <td>2019-12-29</td>\n",
              "      <td>The year of flexi habits The year of self Less...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1824</th>\n",
              "      <td>2019-12-30</td>\n",
              "      <td>Kareena Kapoor is holidaying in Switzerland an...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1825</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>herbal weight loss supplements to help you get...</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1826 rows × 2 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           Date                                               News\n",
              "0    2015-01-01  Breakfast recipe for diabetics: Moong idlis Re...\n",
              "1    2015-01-02  Drink smart with these party tips How to say s...\n",
              "2    2015-01-03  3 Stylish New Year cocktail recipes you'll LOV...\n",
              "3    2015-01-04  How to get that bikini body Rules of love-maki...\n",
              "4    2015-01-05  Recipe: Strawberry cupcakes Recipe: Kaju jeera...\n",
              "...         ...                                                ...\n",
              "1821 2019-12-27  All schools in Naintial to be closed for two d...\n",
              "1822 2019-12-28  Shivin Narang on Bigg Boss 13 contestants: It'...\n",
              "1823 2019-12-29  The year of flexi habits The year of self Less...\n",
              "1824 2019-12-30  Kareena Kapoor is holidaying in Switzerland an...\n",
              "1825 2019-12-31  herbal weight loss supplements to help you get...\n",
              "\n",
              "[1826 rows x 2 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 47
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "rO5_oVqxnEP2",
        "outputId": "ca0706b6-24ae-40ef-a10d-8898a75b3851",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 221
        }
      },
      "source": [
        "df_news['News']"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "0       Breakfast recipe for diabetics: Moong idlis Re...\n",
              "1       Drink smart with these party tips How to say s...\n",
              "2       3 Stylish New Year cocktail recipes you'll LOV...\n",
              "3       How to get that bikini body Rules of love-maki...\n",
              "4       Recipe: Strawberry cupcakes Recipe: Kaju jeera...\n",
              "                              ...                        \n",
              "1821    All schools in Naintial to be closed for two d...\n",
              "1822    Shivin Narang on Bigg Boss 13 contestants: It'...\n",
              "1823    The year of flexi habits The year of self Less...\n",
              "1824    Kareena Kapoor is holidaying in Switzerland an...\n",
              "1825    herbal weight loss supplements to help you get...\n",
              "Name: News, Length: 1826, dtype: object"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 48
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Cleaning headlines*"
      ],
      "metadata": {
        "id": "1QD3dGJTmSr5"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "C919a2Ro_nQ-"
      },
      "source": [
        "c = []\n",
        "for i in range(0,len(df_news['News'])):\n",
        "    news = re.sub('[^a-zA-Z]',' ',df_news['News'][i])\n",
        "    news = news.lower()\n",
        "    news = news.split()\n",
        "    news = [ps.stem(word) for word in news if not word in set(stopwords.words('english'))]\n",
        "    news=' '.join(news)\n",
        "    c.append(news)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "yXiDeuZTSoCu",
        "outputId": "001f65de-9b35-44a0-947b-d6fd44b1585b",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "df_news['News'] = pd.Series(c)\n",
        "df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>News</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>breakfast recip diabet moong idli recip delici...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-02</td>\n",
              "      <td>drink smart parti tip say sorri kid take child...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-03</td>\n",
              "      <td>stylish new year cocktail recip love dessert r...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-04</td>\n",
              "      <td>get bikini bodi rule love make appli work bake...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-05</td>\n",
              "      <td>recip strawberri cupcak recip kaju jeera rice ...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1821</th>\n",
              "      <td>2019-12-27</td>\n",
              "      <td>school naintial close two day nirbhay wadhwa u...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1822</th>\n",
              "      <td>2019-12-28</td>\n",
              "      <td>shivin narang bigg boss contest crazi peopl be...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1823</th>\n",
              "      <td>2019-12-29</td>\n",
              "      <td>year flexi habit year self lesson learnt confu...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1824</th>\n",
              "      <td>2019-12-30</td>\n",
              "      <td>kareena kapoor holiday switzerland internet st...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1825</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>herbal weight loss supplement help get back sh...</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1826 rows × 2 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           Date                                               News\n",
              "0    2015-01-01  breakfast recip diabet moong idli recip delici...\n",
              "1    2015-01-02  drink smart parti tip say sorri kid take child...\n",
              "2    2015-01-03  stylish new year cocktail recip love dessert r...\n",
              "3    2015-01-04  get bikini bodi rule love make appli work bake...\n",
              "4    2015-01-05  recip strawberri cupcak recip kaju jeera rice ...\n",
              "...         ...                                                ...\n",
              "1821 2019-12-27  school naintial close two day nirbhay wadhwa u...\n",
              "1822 2019-12-28  shivin narang bigg boss contest crazi peopl be...\n",
              "1823 2019-12-29  year flexi habit year self lesson learnt confu...\n",
              "1824 2019-12-30  kareena kapoor holiday switzerland internet st...\n",
              "1825 2019-12-31  herbal weight loss supplement help get back sh...\n",
              "\n",
              "[1826 rows x 2 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 50
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Functions to get the subjectivity and polarity*"
      ],
      "metadata": {
        "id": "C56G6pSDmZla"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "EHXwfi5p-ItO"
      },
      "source": [
        "def getSubjectivity(text):\n",
        "  return TextBlob(text).sentiment.subjectivity\n",
        "\n",
        "def getPolarity(text):\n",
        "  return  TextBlob(text).sentiment.polarity"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Adding subjectivity and polarity columns*"
      ],
      "metadata": {
        "id": "tgE2x6KimdCx"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "hD75mMdf-Rsv",
        "outputId": "1f1774e7-9c6f-42b9-befe-05f6d9919e44",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "df_news['Subjectivity'] = df_news['News'].apply(getSubjectivity)\n",
        "df_news['Polarity'] = df_news['News'].apply(getPolarity)\n",
        "df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>News</th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>breakfast recip diabet moong idli recip delici...</td>\n",
              "      <td>0.432083</td>\n",
              "      <td>0.083989</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-02</td>\n",
              "      <td>drink smart parti tip say sorri kid take child...</td>\n",
              "      <td>0.427002</td>\n",
              "      <td>0.059439</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-03</td>\n",
              "      <td>stylish new year cocktail recip love dessert r...</td>\n",
              "      <td>0.412459</td>\n",
              "      <td>0.072817</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-04</td>\n",
              "      <td>get bikini bodi rule love make appli work bake...</td>\n",
              "      <td>0.407143</td>\n",
              "      <td>0.089526</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-05</td>\n",
              "      <td>recip strawberri cupcak recip kaju jeera rice ...</td>\n",
              "      <td>0.409543</td>\n",
              "      <td>0.119753</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1821</th>\n",
              "      <td>2019-12-27</td>\n",
              "      <td>school naintial close two day nirbhay wadhwa u...</td>\n",
              "      <td>0.357721</td>\n",
              "      <td>0.058962</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1822</th>\n",
              "      <td>2019-12-28</td>\n",
              "      <td>shivin narang bigg boss contest crazi peopl be...</td>\n",
              "      <td>0.375351</td>\n",
              "      <td>0.062086</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1823</th>\n",
              "      <td>2019-12-29</td>\n",
              "      <td>year flexi habit year self lesson learnt confu...</td>\n",
              "      <td>0.425854</td>\n",
              "      <td>0.050875</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1824</th>\n",
              "      <td>2019-12-30</td>\n",
              "      <td>kareena kapoor holiday switzerland internet st...</td>\n",
              "      <td>0.384306</td>\n",
              "      <td>0.060182</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1825</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>herbal weight loss supplement help get back sh...</td>\n",
              "      <td>0.374378</td>\n",
              "      <td>0.023247</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1826 rows × 4 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           Date  ...  Polarity\n",
              "0    2015-01-01  ...  0.083989\n",
              "1    2015-01-02  ...  0.059439\n",
              "2    2015-01-03  ...  0.072817\n",
              "3    2015-01-04  ...  0.089526\n",
              "4    2015-01-05  ...  0.119753\n",
              "...         ...  ...       ...\n",
              "1821 2019-12-27  ...  0.058962\n",
              "1822 2019-12-28  ...  0.062086\n",
              "1823 2019-12-29  ...  0.050875\n",
              "1824 2019-12-30  ...  0.060182\n",
              "1825 2019-12-31  ...  0.023247\n",
              "\n",
              "[1826 rows x 4 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 52
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "sKLOKZXg_rP0",
        "outputId": "d893f213-2a39-4035-d313-d3dd0daea323",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 392
        }
      },
      "source": [
        "plt.figure(figsize = (10,6))\n",
        "df_news['Polarity'].hist(color = 'purple')"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "<matplotlib.axes._subplots.AxesSubplot at 0x7f22787c2e10>"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 53
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "H9Q2gW9oBW6G",
        "outputId": "4654c53f-f280-4fa9-ab96-a4829283df27",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 391
        }
      },
      "source": [
        "plt.figure(figsize = (10,6))\n",
        "df_news['Subjectivity'].hist(color = 'blue')"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "<matplotlib.axes._subplots.AxesSubplot at 0x7f22787c2080>"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 54
        },
        {
          "output_type": "display_data",
          "data": {
            "image/png": "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\n",
            "text/plain": [
              "<Figure size 720x432 with 1 Axes>"
            ]
          },
          "metadata": {
            "tags": [],
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "*Adding sentiment score to df_news*"
      ],
      "metadata": {
        "id": "wI0nGnV-mgSh"
      }
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "KhpC_kViBfEM",
        "outputId": "d8a01974-a96e-46b0-b609-ef80792a8a96",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "sia = SentimentIntensityAnalyzer()\n",
        "\n",
        "df_news['Compound'] = [sia.polarity_scores(v)['compound'] for v in df_news['News']]\n",
        "df_news['Negative'] = [sia.polarity_scores(v)['neg'] for v in df_news['News']]\n",
        "df_news['Neutral'] = [sia.polarity_scores(v)['neu'] for v in df_news['News']]\n",
        "df_news['Positive'] = [sia.polarity_scores(v)['pos'] for v in df_news['News']]\n",
        "df_news"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>News</th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>2015-01-01</td>\n",
              "      <td>breakfast recip diabet moong idli recip delici...</td>\n",
              "      <td>0.432083</td>\n",
              "      <td>0.083989</td>\n",
              "      <td>-0.9991</td>\n",
              "      <td>0.118</td>\n",
              "      <td>0.792</td>\n",
              "      <td>0.091</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-02</td>\n",
              "      <td>drink smart parti tip say sorri kid take child...</td>\n",
              "      <td>0.427002</td>\n",
              "      <td>0.059439</td>\n",
              "      <td>-0.9991</td>\n",
              "      <td>0.128</td>\n",
              "      <td>0.768</td>\n",
              "      <td>0.103</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-03</td>\n",
              "      <td>stylish new year cocktail recip love dessert r...</td>\n",
              "      <td>0.412459</td>\n",
              "      <td>0.072817</td>\n",
              "      <td>-0.9996</td>\n",
              "      <td>0.127</td>\n",
              "      <td>0.787</td>\n",
              "      <td>0.086</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-04</td>\n",
              "      <td>get bikini bodi rule love make appli work bake...</td>\n",
              "      <td>0.407143</td>\n",
              "      <td>0.089526</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.131</td>\n",
              "      <td>0.796</td>\n",
              "      <td>0.073</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-05</td>\n",
              "      <td>recip strawberri cupcak recip kaju jeera rice ...</td>\n",
              "      <td>0.409543</td>\n",
              "      <td>0.119753</td>\n",
              "      <td>-0.9989</td>\n",
              "      <td>0.122</td>\n",
              "      <td>0.781</td>\n",
              "      <td>0.097</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1821</th>\n",
              "      <td>2019-12-27</td>\n",
              "      <td>school naintial close two day nirbhay wadhwa u...</td>\n",
              "      <td>0.357721</td>\n",
              "      <td>0.058962</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.179</td>\n",
              "      <td>0.756</td>\n",
              "      <td>0.065</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1822</th>\n",
              "      <td>2019-12-28</td>\n",
              "      <td>shivin narang bigg boss contest crazi peopl be...</td>\n",
              "      <td>0.375351</td>\n",
              "      <td>0.062086</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.170</td>\n",
              "      <td>0.769</td>\n",
              "      <td>0.061</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1823</th>\n",
              "      <td>2019-12-29</td>\n",
              "      <td>year flexi habit year self lesson learnt confu...</td>\n",
              "      <td>0.425854</td>\n",
              "      <td>0.050875</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.163</td>\n",
              "      <td>0.758</td>\n",
              "      <td>0.079</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1824</th>\n",
              "      <td>2019-12-30</td>\n",
              "      <td>kareena kapoor holiday switzerland internet st...</td>\n",
              "      <td>0.384306</td>\n",
              "      <td>0.060182</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.167</td>\n",
              "      <td>0.757</td>\n",
              "      <td>0.076</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1825</th>\n",
              "      <td>2019-12-31</td>\n",
              "      <td>herbal weight loss supplement help get back sh...</td>\n",
              "      <td>0.374378</td>\n",
              "      <td>0.023247</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.185</td>\n",
              "      <td>0.749</td>\n",
              "      <td>0.067</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1826 rows × 8 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           Date  ... Positive\n",
              "0    2015-01-01  ...    0.091\n",
              "1    2015-01-02  ...    0.103\n",
              "2    2015-01-03  ...    0.086\n",
              "3    2015-01-04  ...    0.073\n",
              "4    2015-01-05  ...    0.097\n",
              "...         ...  ...      ...\n",
              "1821 2019-12-27  ...    0.065\n",
              "1822 2019-12-28  ...    0.061\n",
              "1823 2019-12-29  ...    0.079\n",
              "1824 2019-12-30  ...    0.076\n",
              "1825 2019-12-31  ...    0.067\n",
              "\n",
              "[1826 rows x 8 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 55
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "z6P2SKvLFY9M",
        "outputId": "5e84f0db-344c-4c7b-c823-8a6fd388fbf8",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 1000
        }
      },
      "source": [
        "df_merge = pd.merge(df_prices, df_news, how='inner', on='Date')\n",
        "df_merge"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Date</th>\n",
              "      <th>Open</th>\n",
              "      <th>High</th>\n",
              "      <th>Low</th>\n",
              "      <th>Close</th>\n",
              "      <th>Adj Close</th>\n",
              "      <th>Volume</th>\n",
              "      <th>News</th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>2015-01-02</td>\n",
              "      <td>27521.279297</td>\n",
              "      <td>27937.470703</td>\n",
              "      <td>27519.259766</td>\n",
              "      <td>27887.900391</td>\n",
              "      <td>27887.900391</td>\n",
              "      <td>7400.0</td>\n",
              "      <td>drink smart parti tip say sorri kid take child...</td>\n",
              "      <td>0.427002</td>\n",
              "      <td>0.059439</td>\n",
              "      <td>-0.9991</td>\n",
              "      <td>0.128</td>\n",
              "      <td>0.768</td>\n",
              "      <td>0.103</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>2015-01-05</td>\n",
              "      <td>27978.429688</td>\n",
              "      <td>28064.490234</td>\n",
              "      <td>27786.849609</td>\n",
              "      <td>27842.320313</td>\n",
              "      <td>27842.320313</td>\n",
              "      <td>9200.0</td>\n",
              "      <td>recip strawberri cupcak recip kaju jeera rice ...</td>\n",
              "      <td>0.409543</td>\n",
              "      <td>0.119753</td>\n",
              "      <td>-0.9989</td>\n",
              "      <td>0.122</td>\n",
              "      <td>0.781</td>\n",
              "      <td>0.097</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2015-01-06</td>\n",
              "      <td>27694.230469</td>\n",
              "      <td>27698.929688</td>\n",
              "      <td>26937.060547</td>\n",
              "      <td>26987.460938</td>\n",
              "      <td>26987.460938</td>\n",
              "      <td>14100.0</td>\n",
              "      <td>turn garden happi teeth jewelleri latest trend...</td>\n",
              "      <td>0.386223</td>\n",
              "      <td>0.046943</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.133</td>\n",
              "      <td>0.790</td>\n",
              "      <td>0.077</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>2015-01-07</td>\n",
              "      <td>26983.429688</td>\n",
              "      <td>27051.599609</td>\n",
              "      <td>26776.119141</td>\n",
              "      <td>26908.820313</td>\n",
              "      <td>26908.820313</td>\n",
              "      <td>12200.0</td>\n",
              "      <td>ex lover friend water diet bad tip stay happi ...</td>\n",
              "      <td>0.386953</td>\n",
              "      <td>0.032975</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.154</td>\n",
              "      <td>0.765</td>\n",
              "      <td>0.081</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2015-01-08</td>\n",
              "      <td>27178.769531</td>\n",
              "      <td>27316.410156</td>\n",
              "      <td>27101.939453</td>\n",
              "      <td>27274.710938</td>\n",
              "      <td>27274.710938</td>\n",
              "      <td>8200.0</td>\n",
              "      <td>home manicur recip spice beetroot disinfect wa...</td>\n",
              "      <td>0.387770</td>\n",
              "      <td>0.076619</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.131</td>\n",
              "      <td>0.788</td>\n",
              "      <td>0.080</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1219</th>\n",
              "      <td>2019-12-23</td>\n",
              "      <td>41548.261719</td>\n",
              "      <td>41701.621094</td>\n",
              "      <td>41474.609375</td>\n",
              "      <td>41642.660156</td>\n",
              "      <td>41642.660156</td>\n",
              "      <td>6200.0</td>\n",
              "      <td>weekli horoscop decemb check predict zodiac si...</td>\n",
              "      <td>0.412031</td>\n",
              "      <td>0.057669</td>\n",
              "      <td>-0.9995</td>\n",
              "      <td>0.143</td>\n",
              "      <td>0.765</td>\n",
              "      <td>0.092</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1220</th>\n",
              "      <td>2019-12-24</td>\n",
              "      <td>41684.511719</td>\n",
              "      <td>41702.980469</td>\n",
              "      <td>41423.070313</td>\n",
              "      <td>41461.261719</td>\n",
              "      <td>41461.261719</td>\n",
              "      <td>4400.0</td>\n",
              "      <td>choker necklac make sassi throwback style rash...</td>\n",
              "      <td>0.401002</td>\n",
              "      <td>0.064272</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.144</td>\n",
              "      <td>0.773</td>\n",
              "      <td>0.082</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1221</th>\n",
              "      <td>2019-12-26</td>\n",
              "      <td>41543.800781</td>\n",
              "      <td>41543.800781</td>\n",
              "      <td>41132.890625</td>\n",
              "      <td>41163.761719</td>\n",
              "      <td>41163.761719</td>\n",
              "      <td>5600.0</td>\n",
              "      <td>nit develop cold storag system store fruit veg...</td>\n",
              "      <td>0.372711</td>\n",
              "      <td>0.039655</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.152</td>\n",
              "      <td>0.779</td>\n",
              "      <td>0.069</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1222</th>\n",
              "      <td>2019-12-27</td>\n",
              "      <td>41297.078125</td>\n",
              "      <td>41611.269531</td>\n",
              "      <td>41264.921875</td>\n",
              "      <td>41575.140625</td>\n",
              "      <td>41575.140625</td>\n",
              "      <td>6100.0</td>\n",
              "      <td>school naintial close two day nirbhay wadhwa u...</td>\n",
              "      <td>0.357721</td>\n",
              "      <td>0.058962</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.179</td>\n",
              "      <td>0.756</td>\n",
              "      <td>0.065</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1223</th>\n",
              "      <td>2019-12-30</td>\n",
              "      <td>41686.269531</td>\n",
              "      <td>41714.730469</td>\n",
              "      <td>41453.378906</td>\n",
              "      <td>41558.000000</td>\n",
              "      <td>41558.000000</td>\n",
              "      <td>5700.0</td>\n",
              "      <td>kareena kapoor holiday switzerland internet st...</td>\n",
              "      <td>0.384306</td>\n",
              "      <td>0.060182</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.167</td>\n",
              "      <td>0.757</td>\n",
              "      <td>0.076</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1224 rows × 14 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "           Date          Open          High  ...  Negative  Neutral  Positive\n",
              "0    2015-01-02  27521.279297  27937.470703  ...     0.128    0.768     0.103\n",
              "1    2015-01-05  27978.429688  28064.490234  ...     0.122    0.781     0.097\n",
              "2    2015-01-06  27694.230469  27698.929688  ...     0.133    0.790     0.077\n",
              "3    2015-01-07  26983.429688  27051.599609  ...     0.154    0.765     0.081\n",
              "4    2015-01-08  27178.769531  27316.410156  ...     0.131    0.788     0.080\n",
              "...         ...           ...           ...  ...       ...      ...       ...\n",
              "1219 2019-12-23  41548.261719  41701.621094  ...     0.143    0.765     0.092\n",
              "1220 2019-12-24  41684.511719  41702.980469  ...     0.144    0.773     0.082\n",
              "1221 2019-12-26  41543.800781  41543.800781  ...     0.152    0.779     0.069\n",
              "1222 2019-12-27  41297.078125  41611.269531  ...     0.179    0.756     0.065\n",
              "1223 2019-12-30  41686.269531  41714.730469  ...     0.167    0.757     0.076\n",
              "\n",
              "[1224 rows x 14 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 56
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Ju9y5HJ4Fe8P",
        "outputId": "7751ee51-e543-42c0-a1e5-eab1770570dd",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 419
        }
      },
      "source": [
        "df = df_merge[['Close','Subjectivity', 'Polarity', 'Compound', 'Negative', 'Neutral' ,'Positive']]\n",
        "df"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Close</th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>27887.900391</td>\n",
              "      <td>0.427002</td>\n",
              "      <td>0.059439</td>\n",
              "      <td>-0.9991</td>\n",
              "      <td>0.128</td>\n",
              "      <td>0.768</td>\n",
              "      <td>0.103</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>27842.320313</td>\n",
              "      <td>0.409543</td>\n",
              "      <td>0.119753</td>\n",
              "      <td>-0.9989</td>\n",
              "      <td>0.122</td>\n",
              "      <td>0.781</td>\n",
              "      <td>0.097</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>26987.460938</td>\n",
              "      <td>0.386223</td>\n",
              "      <td>0.046943</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.133</td>\n",
              "      <td>0.790</td>\n",
              "      <td>0.077</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>26908.820313</td>\n",
              "      <td>0.386953</td>\n",
              "      <td>0.032975</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.154</td>\n",
              "      <td>0.765</td>\n",
              "      <td>0.081</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>27274.710938</td>\n",
              "      <td>0.387770</td>\n",
              "      <td>0.076619</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.131</td>\n",
              "      <td>0.788</td>\n",
              "      <td>0.080</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>...</th>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "      <td>...</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1219</th>\n",
              "      <td>41642.660156</td>\n",
              "      <td>0.412031</td>\n",
              "      <td>0.057669</td>\n",
              "      <td>-0.9995</td>\n",
              "      <td>0.143</td>\n",
              "      <td>0.765</td>\n",
              "      <td>0.092</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1220</th>\n",
              "      <td>41461.261719</td>\n",
              "      <td>0.401002</td>\n",
              "      <td>0.064272</td>\n",
              "      <td>-0.9998</td>\n",
              "      <td>0.144</td>\n",
              "      <td>0.773</td>\n",
              "      <td>0.082</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1221</th>\n",
              "      <td>41163.761719</td>\n",
              "      <td>0.372711</td>\n",
              "      <td>0.039655</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.152</td>\n",
              "      <td>0.779</td>\n",
              "      <td>0.069</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1222</th>\n",
              "      <td>41575.140625</td>\n",
              "      <td>0.357721</td>\n",
              "      <td>0.058962</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.179</td>\n",
              "      <td>0.756</td>\n",
              "      <td>0.065</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1223</th>\n",
              "      <td>41558.000000</td>\n",
              "      <td>0.384306</td>\n",
              "      <td>0.060182</td>\n",
              "      <td>-0.9999</td>\n",
              "      <td>0.167</td>\n",
              "      <td>0.757</td>\n",
              "      <td>0.076</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>1224 rows × 7 columns</p>\n",
              "</div>"
            ],
            "text/plain": [
              "             Close  Subjectivity  Polarity  ...  Negative  Neutral  Positive\n",
              "0     27887.900391      0.427002  0.059439  ...     0.128    0.768     0.103\n",
              "1     27842.320313      0.409543  0.119753  ...     0.122    0.781     0.097\n",
              "2     26987.460938      0.386223  0.046943  ...     0.133    0.790     0.077\n",
              "3     26908.820313      0.386953  0.032975  ...     0.154    0.765     0.081\n",
              "4     27274.710938      0.387770  0.076619  ...     0.131    0.788     0.080\n",
              "...            ...           ...       ...  ...       ...      ...       ...\n",
              "1219  41642.660156      0.412031  0.057669  ...     0.143    0.765     0.092\n",
              "1220  41461.261719      0.401002  0.064272  ...     0.144    0.773     0.082\n",
              "1221  41163.761719      0.372711  0.039655  ...     0.152    0.779     0.069\n",
              "1222  41575.140625      0.357721  0.058962  ...     0.179    0.756     0.065\n",
              "1223  41558.000000      0.384306  0.060182  ...     0.167    0.757     0.076\n",
              "\n",
              "[1224 rows x 7 columns]"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 57
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "9cr5VpvHxOHV",
        "outputId": "0572f964-b3a4-49bd-b524-8ac4197cf9ae",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 204
        }
      },
      "source": [
        "from sklearn.preprocessing import MinMaxScaler\n",
        "sc = MinMaxScaler()\n",
        "new_df = pd.DataFrame(sc.fit_transform(df))\n",
        "new_df.columns = df.columns\n",
        "new_df.index = df.index\n",
        "new_df.head()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Close</th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>0.263542</td>\n",
              "      <td>0.467934</td>\n",
              "      <td>0.228278</td>\n",
              "      <td>0.00045</td>\n",
              "      <td>0.361702</td>\n",
              "      <td>0.444444</td>\n",
              "      <td>0.478261</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>0.261109</td>\n",
              "      <td>0.387016</td>\n",
              "      <td>0.417200</td>\n",
              "      <td>0.00055</td>\n",
              "      <td>0.297872</td>\n",
              "      <td>0.588889</td>\n",
              "      <td>0.413043</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>0.215467</td>\n",
              "      <td>0.278933</td>\n",
              "      <td>0.189137</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.414894</td>\n",
              "      <td>0.688889</td>\n",
              "      <td>0.195652</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>0.211268</td>\n",
              "      <td>0.282318</td>\n",
              "      <td>0.145385</td>\n",
              "      <td>0.00005</td>\n",
              "      <td>0.638298</td>\n",
              "      <td>0.411111</td>\n",
              "      <td>0.239130</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>0.230803</td>\n",
              "      <td>0.286106</td>\n",
              "      <td>0.282091</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.393617</td>\n",
              "      <td>0.666667</td>\n",
              "      <td>0.228261</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>"
            ],
            "text/plain": [
              "      Close  Subjectivity  Polarity  Compound  Negative   Neutral  Positive\n",
              "0  0.263542      0.467934  0.228278   0.00045  0.361702  0.444444  0.478261\n",
              "1  0.261109      0.387016  0.417200   0.00055  0.297872  0.588889  0.413043\n",
              "2  0.215467      0.278933  0.189137   0.00010  0.414894  0.688889  0.195652\n",
              "3  0.211268      0.282318  0.145385   0.00005  0.638298  0.411111  0.239130\n",
              "4  0.230803      0.286106  0.282091   0.00010  0.393617  0.666667  0.228261"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 58
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "8YshU5poFne8"
      },
      "source": [
        "X = new_df.drop('Close', axis=1)\n",
        "y =new_df['Close']"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "qVA12l3w1SoS",
        "outputId": "2eff1014-fbb8-4ad0-d1c5-ea8c1761ec6c",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 204
        }
      },
      "source": [
        "X.head()"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>0.467934</td>\n",
              "      <td>0.228278</td>\n",
              "      <td>0.00045</td>\n",
              "      <td>0.361702</td>\n",
              "      <td>0.444444</td>\n",
              "      <td>0.478261</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>0.387016</td>\n",
              "      <td>0.417200</td>\n",
              "      <td>0.00055</td>\n",
              "      <td>0.297872</td>\n",
              "      <td>0.588889</td>\n",
              "      <td>0.413043</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>0.278933</td>\n",
              "      <td>0.189137</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.414894</td>\n",
              "      <td>0.688889</td>\n",
              "      <td>0.195652</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>0.282318</td>\n",
              "      <td>0.145385</td>\n",
              "      <td>0.00005</td>\n",
              "      <td>0.638298</td>\n",
              "      <td>0.411111</td>\n",
              "      <td>0.239130</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>0.286106</td>\n",
              "      <td>0.282091</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.393617</td>\n",
              "      <td>0.666667</td>\n",
              "      <td>0.228261</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>"
            ],
            "text/plain": [
              "   Subjectivity  Polarity  Compound  Negative   Neutral  Positive\n",
              "0      0.467934  0.228278   0.00045  0.361702  0.444444  0.478261\n",
              "1      0.387016  0.417200   0.00055  0.297872  0.588889  0.413043\n",
              "2      0.278933  0.189137   0.00010  0.414894  0.688889  0.195652\n",
              "3      0.282318  0.145385   0.00005  0.638298  0.411111  0.239130\n",
              "4      0.286106  0.282091   0.00010  0.393617  0.666667  0.228261"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 60
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "SywSn2Zpxv_0",
        "outputId": "dea48c8b-dda8-46c0-f74d-41f9fa3e89be",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 34
        }
      },
      "source": [
        "x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state = 0)\n",
        "x_train.shape"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "(979, 6)"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 61
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Z9ITxNN41W7a",
        "outputId": "a65bb65b-6075-424a-e054-048df50e0e75",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 359
        }
      },
      "source": [
        "x_train[:10]"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/html": [
              "<div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Subjectivity</th>\n",
              "      <th>Polarity</th>\n",
              "      <th>Compound</th>\n",
              "      <th>Negative</th>\n",
              "      <th>Neutral</th>\n",
              "      <th>Positive</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>359</th>\n",
              "      <td>0.220204</td>\n",
              "      <td>0.281086</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.553191</td>\n",
              "      <td>0.300000</td>\n",
              "      <td>0.434783</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>743</th>\n",
              "      <td>0.358505</td>\n",
              "      <td>0.271120</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.574468</td>\n",
              "      <td>0.333333</td>\n",
              "      <td>0.380435</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>681</th>\n",
              "      <td>0.259733</td>\n",
              "      <td>0.160630</td>\n",
              "      <td>0.00005</td>\n",
              "      <td>0.617021</td>\n",
              "      <td>0.422222</td>\n",
              "      <td>0.250000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>901</th>\n",
              "      <td>0.307586</td>\n",
              "      <td>0.402011</td>\n",
              "      <td>0.00040</td>\n",
              "      <td>0.255319</td>\n",
              "      <td>0.622222</td>\n",
              "      <td>0.423913</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>548</th>\n",
              "      <td>0.183069</td>\n",
              "      <td>0.188262</td>\n",
              "      <td>0.00010</td>\n",
              "      <td>0.382979</td>\n",
              "      <td>0.722222</td>\n",
              "      <td>0.195652</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>904</th>\n",
              "      <td>0.015381</td>\n",
              "      <td>0.263394</td>\n",
              "      <td>0.00005</td>\n",
              "      <td>0.542553</td>\n",
              "      <td>0.511111</td>\n",
              "      <td>0.239130</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1039</th>\n",
              "      <td>0.541122</td>\n",
              "      <td>0.543801</td>\n",
              "      <td>0.00025</td>\n",
              "      <td>0.414894</td>\n",
              "      <td>0.522222</td>\n",
              "      <td>0.358696</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>467</th>\n",
              "      <td>0.205376</td>\n",
              "      <td>0.357111</td>\n",
              "      <td>0.00005</td>\n",
              "      <td>0.638298</td>\n",
              "      <td>0.422222</td>\n",
              "      <td>0.228261</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>85</th>\n",
              "      <td>0.324463</td>\n",
              "      <td>0.347983</td>\n",
              "      <td>0.00015</td>\n",
              "      <td>0.510638</td>\n",
              "      <td>0.422222</td>\n",
              "      <td>0.358696</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>192</th>\n",
              "      <td>0.435857</td>\n",
              "      <td>0.479194</td>\n",
              "      <td>0.00065</td>\n",
              "      <td>0.340426</td>\n",
              "      <td>0.411111</td>\n",
              "      <td>0.543478</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>"
            ],
            "text/plain": [
              "      Subjectivity  Polarity  Compound  Negative   Neutral  Positive\n",
              "359       0.220204  0.281086   0.00010  0.553191  0.300000  0.434783\n",
              "743       0.358505  0.271120   0.00010  0.574468  0.333333  0.380435\n",
              "681       0.259733  0.160630   0.00005  0.617021  0.422222  0.250000\n",
              "901       0.307586  0.402011   0.00040  0.255319  0.622222  0.423913\n",
              "548       0.183069  0.188262   0.00010  0.382979  0.722222  0.195652\n",
              "904       0.015381  0.263394   0.00005  0.542553  0.511111  0.239130\n",
              "1039      0.541122  0.543801   0.00025  0.414894  0.522222  0.358696\n",
              "467       0.205376  0.357111   0.00005  0.638298  0.422222  0.228261\n",
              "85        0.324463  0.347983   0.00015  0.510638  0.422222  0.358696\n",
              "192       0.435857  0.479194   0.00065  0.340426  0.411111  0.543478"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 62
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "8Xb3p5PsyCxL"
      },
      "source": [
        "rf = RandomForestRegressor()\n",
        "rf.fit(x_train, y_train)\n",
        "prediction=rf.predict(x_test)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "QQ44SlYU0_tV",
        "outputId": "8ebe0446-762b-4ce3-c35a-ac699007e551",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 255
        }
      },
      "source": [
        "print(prediction[:10])\n",
        "print(y_test[:10])\n",
        "print(mean_squared_error(prediction,y_test))"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "[0.56900133 0.65381814 0.40213346 0.41479438 0.5887583  0.52221731\n",
            " 0.57886431 0.73085042 0.59790365 0.58271874]\n",
            "853     0.658574\n",
            "5       0.240610\n",
            "912     0.788380\n",
            "124     0.280673\n",
            "998     0.720396\n",
            "941     0.593473\n",
            "1074    0.875659\n",
            "989     0.708024\n",
            "52      0.283467\n",
            "529     0.309214\n",
            "Name: Close, dtype: float64\n",
            "0.044734065297256\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "nq2Ugkav6kf1"
      },
      "source": [
        "adb = AdaBoostRegressor()\n",
        "adb.fit(x_train, y_train)\n",
        "predictions = adb.predict(x_test)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "nkc1C3tt6wIO",
        "outputId": "75e3b1c2-299f-4c79-cb36-fed9a47f762c",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 34
        }
      },
      "source": [
        "print(mean_squared_error(predictions, y_test))"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "0.049009608942640545\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "eaSqTmPq29Aj"
      },
      "source": [
        "from sklearn.tree import DecisionTreeRegressor\n",
        "dec_tree = DecisionTreeRegressor()\n",
        "dec_tree.fit(x_train, y_train)\n",
        "predictions = dec_tree.predict(x_test)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "0duwvBkP3bdY",
        "outputId": "bff03515-d922-4072-c8af-53e491b4abef",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 255
        }
      },
      "source": [
        "print(predictions[:10])\n",
        "print(y_test[:10])\n",
        "print(mean_squared_error(predictions,y_test))"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "[0.79743516 0.92551627 0.52952711 0.22954278 0.72776935 0.64877199\n",
            " 0.69289974 0.80251691 0.05927529 0.72557448]\n",
            "853     0.658574\n",
            "5       0.240610\n",
            "912     0.788380\n",
            "124     0.280673\n",
            "998     0.720396\n",
            "941     0.593473\n",
            "1074    0.875659\n",
            "989     0.708024\n",
            "52      0.283467\n",
            "529     0.309214\n",
            "Name: Close, dtype: float64\n",
            "0.09108309698560868\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "RZ-BFaZP4rWA",
        "outputId": "10f37410-ae0c-4de7-db8f-83bef0ccabfe",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 119
        }
      },
      "source": [
        "lgb = lightgbm.LGBMRegressor()\n",
        "lgb.fit(x_train, y_train)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "LGBMRegressor(boosting_type='gbdt', class_weight=None, colsample_bytree=1.0,\n",
              "              importance_type='split', learning_rate=0.1, max_depth=-1,\n",
              "              min_child_samples=20, min_child_weight=0.001, min_split_gain=0.0,\n",
              "              n_estimators=100, n_jobs=-1, num_leaves=31, objective=None,\n",
              "              random_state=None, reg_alpha=0.0, reg_lambda=0.0, silent=True,\n",
              "              subsample=1.0, subsample_for_bin=200000, subsample_freq=0)"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 69
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "rRJ1D-ba5W7B",
        "outputId": "085ce566-a95c-4960-c39a-cd0733237b05",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 34
        }
      },
      "source": [
        "predictions = lgb.predict(x_test)\n",
        "print(mean_squared_error(predictions,y_test))"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "0.050222162630987485\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "OIdi44Xm5_uR",
        "outputId": "1a81aa3e-e2ce-437a-a8c8-604c9e846097",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 173
        }
      },
      "source": [
        "xgb = xgboost.XGBRegressor()\n",
        "xgb.fit(x_train, y_train)"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "[10:50:54] WARNING: /workspace/src/objective/regression_obj.cu:152: reg:linear is now deprecated in favor of reg:squarederror.\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "XGBRegressor(base_score=0.5, booster='gbtree', colsample_bylevel=1,\n",
              "             colsample_bynode=1, colsample_bytree=1, gamma=0,\n",
              "             importance_type='gain', learning_rate=0.1, max_delta_step=0,\n",
              "             max_depth=3, min_child_weight=1, missing=None, n_estimators=100,\n",
              "             n_jobs=1, nthread=None, objective='reg:linear', random_state=0,\n",
              "             reg_alpha=0, reg_lambda=1, scale_pos_weight=1, seed=None,\n",
              "             silent=None, subsample=1, verbosity=1)"
            ]
          },
          "metadata": {
            "tags": []
          },
          "execution_count": 71
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "AxhH9EJ16Wkz",
        "outputId": "a792212f-0a8a-418d-c861-52fb7d90a654",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 34
        }
      },
      "source": [
        "predictions = xgb.predict(x_test)\n",
        "print(mean_squared_error(predictions,y_test))"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "0.043341490145982466\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "RNeRBjhVXcIS"
      },
      "source": [
        "**Conclusion**\n",
        "\n",
        "We observe that Xgboost model performs the best for the sentiment analysis"
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Thankyou**\n",
        "\n",
        "\n",
        "---\n",
        "\n",
        "\n",
        "\n",
        "---\n",
        "\n"
      ],
      "metadata": {
        "id": "q97UqdgUmkHD"
      }
    }
  ]
}
