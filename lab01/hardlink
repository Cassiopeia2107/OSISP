#define _GNU_SOURCE
#include <stdio.h>
#include <dirent.h>
#include <stdlib.h>
#include <string.h>
#include <getopt.h>
#include <sys/stat.h>

#define MAX_LENGHT 255
#define SLASH "/"
//dir - 4, link - 10, file - 8

void set_options(int argc, char** argv, int** opt);
void find_path(int argc, char** argv, char** file_path);
void dir_walk(char*** all_files_list, char* file_path, int* options, int* counter);
void add_file_to_list(char*** all_files_list, char* file_type, char* file_name, int *counter);
void print_result(char** all_files_list, int counter);
void sort_list(char*** all_files_list, int counter);

int main(int argc, char** argv ) {
    int* options = (int*)calloc(4, sizeof(int));
    char** all_files_list = (char**)calloc(1, sizeof(char*));
    char* file_path = (char*)malloc(MAX_LENGHT * sizeof(char));
    int counter = 0;
    set_options(argc, argv, &options);
    find_path(argc, argv, &file_path);
    dir_walk(&all_files_list, file_path, options, &counter);
    if(options[3]) sort_list(&all_files_list, counter);
    print_result(all_files_list, counter);
    return 0;
}

void set_options(int argc, char** argv, int** opt) {
    int buff;                                          //буфер для чтения параметров
    while ((buff = getopt(argc, argv, "-fdls")) != -1) {    //чтение
        switch (buff) {
            case 'f':
                (*opt)[0] = 1;
                break;              //установка флага файлов
            case 'd':
                (*opt)[1] = 1;
                break;              //установка флага директорий
            case 'l':
                (*opt)[2] = 1;
                break;              //установка флага символических ссылок
            case 's':
                (*opt)[3] = 1;
                break;              //флаг сортировки параметров
            case '?':
                exit(-1);     //случай невереного параметра
            default:
                break;
        }
    }
    if(!(*opt)[0] && !(*opt)[1] && !(*opt)[2]){
        (*opt)[0] = 1;
        (*opt)[1] = 1;
        (*opt)[2] = 1;
    }
}

void dir_walk(char*** all_files_list, char* file_path, int* options, int* counter) {
    DIR *dir;
    struct stat dir_info;
    dir = opendir(file_path);
    if (dir) {
        struct dirent *entry;
        while((entry = readdir(dir)) != NULL){
            if(!(!strcmp(entry->d_name, ".") || !strcmp(entry->d_name, ".."))) {
                char next_directory[MAX_LENGHT];
                strcpy(next_directory, file_path);
                strcat(next_directory, SLASH);
                strcat(next_directory, entry->d_name);
                lstat(next_directory, &dir_info);
                if (S_ISDIR(dir_info.st_mode)) {
                    dir_walk(all_files_list, next_directory, options, counter);
                }
                if (S_ISDIR(dir_info.st_mode) && options[1]) {
                    add_file_to_list(all_files_list, "d - ", next_directory, counter);
                }
                if (S_ISREG(dir_info.st_mode) && options[0]) {
                    add_file_to_list(all_files_list, "f - ", next_directory, counter);
                }
                if (S_ISLNK(dir_info.st_mode) && options[2]) {
                    add_file_to_list(all_files_list, "l - ", next_directory, counter);
                }
            }
        }

        if(closedir(dir)){
            fprintf(stderr, "Error,not closed %s\n", file_path);
        }
    } else {
        fprintf(stderr, "Error,not opened %s\n", file_path);
        return;
    }
}


void print_result(char** all_files_list, int counter){
    for(int i = 0; i < counter; i++)
        printf("%s\n", *(all_files_list + i));
    free(all_files_list);
}

void sort_list(char*** all_files_list, int counter){
    for (int i = 0; i < counter - 1; ++i) {
        for (int j = 0; j < counter - i - 1; ++j) {
            //алфавитное сравнение строк
            if (strcoll((*all_files_list)[j], (*all_files_list)[j + 1]) > 0) {
                char* tempBuf = (*all_files_list)[j];
                (*all_files_list)[j] = (*all_files_list)[j + 1];
                (*all_files_list)[j + 1] = tempBuf;
            }
        }
    }
}

void add_file_to_list(char*** all_files_list, char* file_type, char* file_name, int *counter){
    (*all_files_list) = (char**)realloc((*all_files_list), sizeof(char*) * (*counter + 1));
    (*all_files_list)[*counter] = (char*)calloc(MAX_LENGHT, sizeof(char));
    strcpy((*all_files_list)[*counter], file_type);
    strcat((*all_files_list)[*counter], file_name);
    (*counter) += 1;
}

void find_path(int argc, char** argv, char** file_path){
        if (argc == 1 || (argv[1][0] == '-' && argv[argc - 1][0] == '-')) {
            strcpy(*file_path, ".");
        } else {
            for (int i = 1; i < argc; i++) {
                if (argv[i][0] != '-'){
                    strcpy(*file_path, argv[i]);
                }
            }
        }
    }
