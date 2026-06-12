// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract WalletActivityChecker {

    // Struktura przechowująca podstawowe informacje o aktywności portfela
    struct WalletInfo {
        uint256 balance;       // Stan konta w natywnej monecie (np. ETH/BNB)
        uint256 nonce;         // Liczba wysłanych transakcji z tego adresu
        bool isContract;       // Czy podany adres to smart kontrakt, czy portfel użytkownika (EOA)
        uint32 codeSize;       // Rozmiar kodu (0 dla zwykłych portfeli)
    }

    /**
     * @notice Sprawdza aktywność i status podanego adresu URL/portfela
     * @param _wallet Adres portfela do sprawdzenia
     * @return WalletInfo Struktura z danymi o aktywności portfela
     */
    function checkActivity(address _wallet) external view returns (WalletInfo memory) {
        require(_wallet != address(0), "Niepoprawny adres portfela");

        uint256 currentBalance = _wallet.balance;
        uint256 transactionCount = _wallet.nonce;
        
        // Sprawdzenie czy podany adres to smart kontrakt
        uint32 size;
        assembly {
            size := extcodesize(_wallet)
        }
        bool contractStatus = (size > 0);

        return WalletInfo({
            balance: currentBalance,
            nonce: transactionCount,
            isContract: contractStatus,
            codeSize: size
        });
    }

    /**
     * @notice Szybkie sprawdzenie, czy portfel kiedykolwiek wysłał jakąś transakcję
     * @param _wallet Adres portfela
     * @return bool Prawda, jeśli portfel wysłał minimum jedną transakcję
     */
    function hasEverSentTransaction(address _wallet) external view returns (bool) {
        return _wallet.nonce > 0;
    }
}
